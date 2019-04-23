---
title: 'Design a Multi-Tenant Database'
description: This tutorial shows how to create, populate, and query distributed tableson Azure Database for PostgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 04/09/2019
---

# Tutorial: Design a Multi-Tenant Database

In this tutorial, you use Azure Database for PostgreSQL Hyperscale
to learn how to:

> [!div class="checklist"]
> * Use psql utility to create a database
> * Shard tables across nodes
> * Ingest sample data
> * Query tenant data
> * Share data between tenants
> * Customize the schema per-tenant

TODO: add section for creating db and getting connection string.

## Use psql utility to create a database

Now that you know how to connect to the Azure Database for PostgreSQL,
you can complete some basic tasks.

First let's create tables for a hypothetical online advertising
application. The application must keep track of multiple companies,
each of which runs advertising campaigns. Campaigns have many ads,
and each ad has associated records of its clicks and impressions.

In the psql client, run these commands:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);

CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);

CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

You can see the newly created tables in the list of tables now by typing:

```postgres
\dt
```

Note how all primary and foreign keys include the company id. This
enforces uniqueness per tenant.

## Shard tables across nodes

Citus stores table rows on different nodes based on the value of a
user-designated column called the "distribution column." This column
marks which tenant owns which rows.

Let's set the distribution column to be company\_id, the tenant
identifier. Run this in psql:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## Ingest sample data

From the command line, download sample data sets:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Back inside psql, bulk load the data:

```postgresql
\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

This data will now be spread across worker nodes.

## Query tenant data

Citus executes single-tenant queries on whichever worker node
contains data for that tenant. For instance, the following query
for company 5 ranks the ads in each campaign by the count of their
impressions.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## Share data between tenants

Up until now all tables have been distributed by `company_id`, but
sometimes there is data that can be shared by all tenants, and
doesn't "belong" to any tenant in particular. For instance, all
companies using this example ad platform might want to get geographical
information for their audience based on IP addresses.

Create a table to hold geographic information:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Next designate `geo_ips` as a "reference table." This stores a copy
of the table on every worker node, for fast lookups by any tenant.

```sql
SELECT create_reference_table('geo_ips');
```

Load it with example data:

```postgresql
\copy geo_ips from 'geo_ips.csv' with csv
```

Joining the clicks with geo\_ips is efficient. We can ask, for
example, the locations of everyone who clicked on ad 290.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## Customize the schema per-tenant

Given that all tenants share a common schema and hardware infrastructure,
how can we allow some tenants to store extra information not needed
by others? One way is through PostgreSQL's JSONB type.

Our schema already has a JSONB field in `clicks` called `user_data`.
Suppose company five includes information in the field to track
whether the user is on a mobile device. The company can query to
find who clicks more, mobile or traditional visitors:

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

We can optimize this query for company five by creating a [partial
index](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

More generally, we can create a [GIN
indices](https://www.postgresql.org/docs/current/static/gin-intro.html) on
every key and value within the column.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```
