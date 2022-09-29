---
title: Use Node.js to connect and query Azure Cosmos DB for PostgreSQL
description: See how to use Node.js to connect and run SQL statements on Azure Cosmos DB for PostgreSQL.
ms.author: sasriram
author: saimicrosoft
ms.service: cosmos-db
ms.subservice: postgresql
ms.topic: quickstart
recommendations: false
ms.date: 09/28/2022
---

# Use Node.js to connect and run SQL commands on Azure Cosmos DB for PostgreSQL

[!INCLUDE [PostgreSQL](../includes/appliesto-postgresql.md)]

This quickstart shows you how to use Node.js code to connect to a cluster, and then use SQL statements to create a table and insert, query, update, and delete data in the database. The steps in this article assume that you're familiar with Node.js development, and are new to working with Azure Cosmos DB for PostgreSQL.

> [!TIP]
> The process of creating a Node.js app with Azure Cosmos DB for PostgreSQL is the same as working with ordinary PostgreSQL.

## Prerequisites

- An Azure account with an active subscription. If you don't have one, [create an account for free](https://azure.microsoft.com/free).
- An Azure Cosmos DB for PostgreSQL cluster. To create a cluster, see [Create a cluster in the Azure portal](quickstart-create-portal.md).
- [Node.js](https://nodejs.org) installed.
- For various samples, the following packages installed:

  - [pg](https://www.npmjs.com/package/pg) PostgreSQL client for Node.js.
  - [pg-copy-streams](https://www.npmjs.com/package/pg-copy-streams)
  - [through2](https://www.npmjs.com/package/through2) to allow pipe chaining.

   Install these packages from your command line by using the JavaScript `npm` node package manager.
  
  ```bash
  npm install <package name>
  ```

  Verify the installation by listing the packages installed.

  ```bash
  npm list
  ```

To run the code in these examples, you can launch Node.js from the Bash shell, terminal, or Windows command prompt by typing `node`. Then run the example JavaScript code interactively by copying and pasting the code into the prompt. Or, you can save the JavaScript code into a *\<filename>.js* file, and then run `node <filename>.js` with the file name as a parameter.

The code samples in this article use your cluster name and password. You can see your cluster name at the top of your cluster page in the Azure portal.

:::image type="content" source="media/howto-app-stacks/cluster-name.png" alt-text="Screenshot of the cluster name in the Azure portal.":::

## Connect, create a table, and insert data

All examples in this article need to connect to the database. You can put the connection logic into its own module for reuse. Use the [pg](https://node-postgres.com) client object to interface with the PostgreSQL server.

[!INCLUDE[why-connection-pooling](includes/why-connection-pooling.md)]

### Create the common connection module

Create a folder called *db*, and inside this folder create a *citus.js* file that contains the following common connection code. In this code, replace \<cluster> with your cluster name and \<password> with your administrator password.

```javascript
/**
* file: db/citus.js
*/

const { Pool } = require('pg');

const pool = new Pool({
  max: 300,
  connectionTimeoutMillis: 5000,

  host: 'c.<cluster>.postgres.database.azure.com',
  port: 5432,
  user: 'citus',
  password: '<password>',
  database: 'citus',
  ssl: true,
});

module.exports = {
  pool,
};
```

### Create a table

Use the following code to connect and load the data by using CREATE TABLE
and INSERT INTO SQL statements. The code creates a new `pharmacy` table and inserts some sample data.

```javascript
/**
* file: create.js
*/

const { pool } = require('./db/citus');

async function queryDatabase() {
  const queryString = `
    DROP TABLE IF EXISTS pharmacy;
    CREATE TABLE pharmacy (pharmacy_id integer,pharmacy_name text,city text,state text,zip_code integer);
    INSERT INTO pharmacy (pharmacy_id,pharmacy_name,city,state,zip_code) VALUES (0,'Target','Sunnyvale','California',94001);
    INSERT INTO pharmacy (pharmacy_id,pharmacy_name,city,state,zip_code) VALUES (1,'CVS','San Francisco','California',94002);
    INSERT INTO pharmacy (pharmacy_id,pharmacy_name,city,state,zip_code) VALUES (2,'Walgreens','San Diego','California',94003);
    CREATE INDEX idx_pharmacy_id ON pharmacy(pharmacy_id);
  `;

  try {
    /* Real application code would probably request a dedicated client with
       pool.connect() and run multiple queries with the client. In this
       example, you're running only one query, so you use the pool.query()
       helper method to run it on the first available idle client.
    */

    await pool.query(queryString);
    console.log('Created the Pharmacy table and inserted rows.');
  } catch (err) {
    console.log(err.stack);
  } finally {
    pool.end();
  }
}

queryDatabase();
```

## Distribute tables

Azure Cosmos DB for PostgreSQL gives you [the super power of distributing tables](overview.md#the-superpower-of-distributed-tables) across multiple nodes for scalability. The command below enables you to distribute a table. You can learn more about `create_distributed_table` and the distribution column [here](quickstart-build-scalable-apps-concepts.md#distribution-column-also-known-as-shard-key).

> [!NOTE]
> Distributing tables has no effect in an Azure Cosmos DB for PostgreSQL cluster with no worker nodes.

Use the following code to connect to the database and distribute the table.

```javascript
/**
* file: distribute-table.js
*/

const { pool } = require('./db/citus');

async function queryDatabase() {
  const queryString = `
    SELECT create_distributed_table('pharmacy', 'pharmacy_id');
  `;

  try {
    await pool.query(queryString);
    console.log('Distributed pharmacy table.');
  } catch (err) {
    console.log(err.stack);
  } finally {
    pool.end();
  }
}

queryDatabase();
```

## Read data

Use the following code to connect and read the data by using a SELECT SQL statement.

```javascript
/**
* file: read.js
*/

const { pool } = require('./db/citus');

async function queryDatabase() {
  const queryString = `
    SELECT * FROM pharmacy;
  `;

  try {
    const res = await pool.query(queryString);
    console.log(res.rows);
  } catch (err) {
    console.log(err.stack);
  } finally {
    pool.end();
  }
}

queryDatabase();
```

## Update data

Use the following code to connect and update the data by using an UPDATE SQL statement.

```javascript
/**
* file: update.js
*/

const { pool } = require('./db/citus');

async function queryDatabase() {
  const queryString = `
    UPDATE pharmacy SET city = 'Long Beach'
    WHERE pharmacy_id = 1;
  `;

  try {
    const result = await pool.query(queryString);
    console.log('Update completed.');
    console.log(`Rows affected: ${result.rowCount}`);
  } catch (err) {
    console.log(err.stack);
  } finally {
    pool.end();
  }
}

queryDatabase();
```

## Delete data

Use the following code to connect and delete the data by using a DELETE SQL statement.

```javascript
/**
* file: delete.js
*/

const { pool } = require('./db/citus');

async function queryDatabase() {
  const queryString = `
    DELETE FROM pharmacy
    WHERE pharmacy_name = 'Target';
  `;

  try {
    const result = await pool.query(queryString);
    console.log('Delete completed.');
    console.log(`Rows affected: ${result.rowCount}`);
  } catch (err) {
    console.log(err.stack);
  } finally {
    pool.end();
  }
}

queryDatabase();
```

## COPY command for fast ingestion

The COPY command can yield [tremendous throughput](https://www.citusdata.com/blog/2016/06/15/copy-postgresql-distributed-tables) while ingesting data into Azure Cosmos DB for PostgreSQL. The COPY command can ingest data in files, or from micro-batches of data in memory for real-time ingestion.

### COPY command to load data from a file

The following code copies data from a CSV file to a database table. The code requires the [pg-copy-streams](https://www.npmjs.com/package/pg-copy-streams) package and the file [pharmacies.csv](https://download.microsoft.com/download/d/8/d/d8d5673e-7cbf-4e13-b3e9-047b05fc1d46/pharmacies.csv).

```javascript
/**
* file: copycsv.js
*/

const inputFile = require('path').join(__dirname, '/pharmacies.csv');
const fileStream = require('fs').createReadStream(inputFile);
const copyFrom = require('pg-copy-streams').from;
const { pool } = require('./db/citus');

async function importCsvDatabase() {
  return new Promise((resolve, reject) => {
    const queryString = `
      COPY pharmacy FROM STDIN WITH (FORMAT CSV, HEADER true, NULL '');
    `;

    fileStream.on('error', reject);

    pool
      .connect()
      .then(client => {
        const stream = client
          .query(copyFrom(queryString))
          .on('error', reject)
          .on('end', () => {
            reject(new Error('Connection closed!'));
          })
          .on('finish', () => {
            client.release();
            resolve();
          });

        fileStream.pipe(stream);
      })
      .catch(err => {
        reject(new Error(err));
      });
  });
}

(async () => {
  console.log('Copying from CSV...');
  await importCsvDatabase();
  await pool.end();
  console.log('Inserted csv successfully');
})();
```

### COPY command to load in-memory data

The following code copies in-memory data to a table. The code requires the [through2](https://www.npmjs.com/package/through2) package, which allows pipe chaining.

```javascript
/**
 * file: copyinmemory.js
 */

const through2 = require('through2');
const copyFrom = require('pg-copy-streams').from;
const { pool } = require('./db/citus');

async function importInMemoryDatabase() {
  return new Promise((resolve, reject) => {
    pool
      .connect()
      .then(client => {
        const stream = client
          .query(copyFrom('COPY pharmacy FROM STDIN'))
          .on('error', reject)
          .on('end', () => {
            reject(new Error('Connection closed!'));
          })
          .on('finish', () => {
            client.release();
            resolve();
          });

        const internDataset = [
          ['100', 'Target', 'Sunnyvale', 'California', '94001'],
          ['101', 'CVS', 'San Francisco', 'California', '94002'],
        ];

        let started = false;
        const internStream = through2.obj((arr, _enc, cb) => {
          const rowText = (started ? '\n' : '') + arr.join('\t');
          started = true;
          cb(null, rowText);
        });

        internStream.on('error', reject).pipe(stream);

        internDataset.forEach((record) => {
          internStream.write(record);
        });

        internStream.end();
      })
      .catch(err => {
        reject(new Error(err));
      });
  });
}
(async () => {
  await importInMemoryDatabase();
  await pool.end();
  console.log('Inserted inmemory data successfully.');
})();
```

## App retry for database request failures

[!INCLUDE[app-stack-next-steps](includes/app-stack-retry-intro.md)]

In this code, replace \<cluster> with your cluster name and \<password> with your administrator password.

```javascript
const { Pool } = require('pg');
const { sleep } = require('sleep');

const pool = new Pool({
  host: 'c.<cluster>.postgres.database.azure.com',
  port: 5432,
  user: 'citus',
  password: '<password>',
  database: 'citus',
  ssl: true,
  connectionTimeoutMillis: 0,
  idleTimeoutMillis: 0,
  min: 10,
  max: 20,
});

(async function() {
  res = await executeRetry('select nonexistent_thing;',5);
  console.log(res);
  process.exit(res ? 0 : 1);
})();

async function executeRetry(sql,retryCount)
{
  for (let i = 0; i < retryCount; i++) {
    try {
      result = await pool.query(sql)
      return result;
    } catch (err) {
      console.log(err.message);
      sleep(60);
    }
  }

  // didn't succeed after all the tries
  return null;
}
```

## Next steps

[!INCLUDE[app-stack-next-steps](includes/app-stack-next-steps.md)]
