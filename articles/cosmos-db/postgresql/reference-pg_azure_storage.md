---
title: pg_azure_storage extension
description: Reference documentation for using Azure Blob Storage extension
ms.author: avijitgupta
author: AvijitkGupta
ms.service: cosmos-db
ms.subservice: postgresql
ms.topic: reference
ms.date: 03/16/2023
---

# pg_azure_storage extension

[!INCLUDE [PostgreSQL](../includes/appliesto-postgresql.md)]

The pg_azure_storage extension provides extensibility, for seamless manipulation and loading of data in different file formats, into your Azure Cosmos DB for PostgreSQL cluster directly from Azure Blob Storage. Containers with access level “Private” or “Blob” requires adding private access key.

You can create the extension from psql by running: <br />
``CREATE EXTENSION azure_storage;``

### Syntax

`COPY FROM` copies data from a file, hosted on a file system or within `Azure blob storage`, to a sql table (appending the data to whatever is in the table already). The command is extremely helpful in dealing with large datasets, significantly reducing the time and resources required for data transfer.

```sql
COPY table_name [ ( column_name [, ...] ) ]
FROM { 'filename' | PROGRAM 'command' | STDIN | Azure_blob_url}
    [ [ WITH ] ( option [, ...] ) ]
    [ WHERE condition ]
```
> [!IMPORTANT]
Syntax and options supported remains likewise to Postgres Native <span style="color:blue;">[COPY](https://www.postgresql.org/docs/current/sql-copy.html)</span>  <br />command, with following exceptions:<br />
> a.	FREEZE [ boolean ]    
> b.	HEADER [ MATCH ]

> [!NOTE] `COPY TO` syntax is yet not supported.

### Arguments 
##### Azure_blob_url
Allows unstructured data to be stored and accessed at a massive scale in block blobs. Objects in blob storage can be accessed from anywhere in the world via HTTP or HTTPS. User or client applications can access blobs via URLs, the Azure Storage REST API, Azure PowerShell, Azure CLI, or an Azure storage client library. The storage client libraries are available for multiple languages, including .NET, Java, Node.js, Python, PHP, and Ruby.

### Option
##### format
Specifies the format of destination file. Currently the extension supports following formats

| **Format** | **Description**                                          |
|------------|----------------------------------------------------------|
| csv        | Comma-separated values format used by PostgreSQL COPY    |
| tsv        | Tab-separated values, the default PostgreSQL COPY format |
| binary     | Binary PostgreSQL COPY format                            |
| text       | A file containing a single text value (for example, large JSON or XML)                            |
|            |                                                         |
 
### Syntax (blob_list)

The function lists the available blob files within a user container with their properties.

```sql
azure_storage.blob_list (account_name , container_name [, prefix ])
Returns Table
	(
 	  path             text
    , bytes            bigint
    , last_modified    timestamp with time zone
    , etag             text
    , content_type     text
    , content_encoding text
    , content_hash     text
    );
```

### Arguments 
##### account_name
The `storage account name` provides a unique namespace for your Azure storage data that's accessible from anywhere in the world over HTTP or HTTPS.
##### container_name
A container organizes a set of blobs, similar to a directory in a file system. A storage account can include an unlimited number of containers, and a container can store an unlimited number of blobs. <br />
> A container name must be a valid DNS name, as it forms part of the unique URI used to address the container or its blobs. Follow these rules when naming a container: <br />
> •	Container names can be between 3 and 63 characters long. <br />
> •	Container names must start with a letter or number, and can contain only lowercase letters, numbers, and the dash (-) character. <br />
> •	Two or more consecutive dash characters aren't permitted in container names.
The URI for a container is similar to: <br />
`https://myaccount.blob.core.windows.net/mycontainer`

##### prefix
returns file from blob container with matching string initials. <br />
##### path (output)
full qualified path of Azure blob directory.
##### bytes (output)
size of file object in bytes.
##### last_modified (output)
when was the file content last modified.
##### etag (output)
An ETag property is used for optimistic concurrency during updates. It is not a timestamp as there is another property called Timestamp that stores the last time a record was updated. For example, if you load an entity and want to update it, the ETag must match what is currently stored. This is important b/c if you have multiple users editing the same item, you don't want them overwriting each other's changes.
##### content_type (output)
The Blob object represents a blob, which is a file-like object of immutable, raw data; they can be read as text or binary data, or converted into a ReadableStream so its methods can be used for processing the data. Blobs can represent data that isn't necessarily in a JavaScript-native format.
##### content_encoding (output)
Azure Storage allows you to define Content-Encoding property on a blob. For compressed content, you could set this property to be GZIP and when this content is served by a browser, it automatically decompresses the content and shows the uncompressed content
##### content_hash (output)
This hash is used to verify the integrity of the blob during transport. When this header is specified, the storage service checks the hash that has arrived with the one that was sent. If the two hashes do not match, the operation will fail with error code 400 (Bad Request).

### Return Type
Table 

> [!NOTE] <span style="color:brown;">Permissions</span>  <br />
Now you can list containers set to Private and Blob access levels for that storage but only as the `citus user`, which has the `azure_storage_admin` role granted to it. If you create a new user named support, it won't be allowed to access container contents by default.

### Syntax (blob_get)
The function allows loading the content of file \ files from within the container, with added support on filtering or manipulation of data, prior to import.

```sql
azure_storage.blob_get 
            ( 'account'
            , 'container'
            , 'path' 
           [, dest_table_structure] 
           [, decoder := 'csv' ] 
           [, compression := 'gzip' ] 
           [, options => azure_storage.options_copy (delimited := '|', header := true)] );
```

### Arguments 

##### account
The storage account provides a unique namespace for your Azure Storage data that's accessible from anywhere in the world over HTTP or HTTPS.
##### container
A container organizes a set of blobs, similar to a directory in a file system. A storage account can include an unlimited number of containers, and a container can store an unlimited number of blobs.
A container name must be a valid DNS name, as it forms part of the unique URI used to address the container or its blobs. 
##### path
Blob name existing in the container.
##### dest_table_structure
structure of destination table
NULL :: table_name  -> returns data file from blob as per the schema of table.

##### decoder
specify the blob format
Decoder can be set to auto (default) or any of the following values
 
##### decoder description
| **Format** | **Description**                                          |
|------------|----------------------------------------------------------|
| csv        | Comma-separated values format used by PostgreSQL COPY    |
| tsv        | Tab-separated values, the default PostgreSQL COPY format |
| binary     | Binary PostgreSQL COPY format                            |
| text       | A file containing a single text value (for example, large JSON or XML)                            |
|            |                                                         |

##### compression 
defines the compression format as either uncompressed or gzip. This would be auto identified by accessing the file format. 
##### options
for handling custom headers, custom separators, escape characters etc, `COPY` command can be passed under `options` to blob_get function.  

### Return Type
Set of Records

> [!NOTE] <span style="color:brown;">Permissions</span>  <br />
Now you can list containers set to Private and Blob access levels for that storage but only as the `citus user`, which has the `azure_storage_admin` role granted to it. If you create a new user named support, it won't be allowed to access container contents by default.


### Syntax (account_add)
Allowing access to a storage account requires adding the storage account.
```sql
azure_storage.account_add ('storage_account', '<base 64 encoded account key>');
```
##### storage_account
An Azure storage account contains all of your Azure Storage data objects: blobs, files, queues, and tables. The storage account provides a unique namespace for your Azure Storage data that is accessible from anywhere in the world over HTTP or HTTPS.

##### account_key
Your storage account access keys are similar to a root password for your storage account. Always be careful to protect your access keys. Use Azure Key Vault to manage and rotate your keys securely. The account key is stored in a table that is only accessible by the postgres superuser. To see which storage accounts exist, use the account_list

##### Syntax
For removing access to storage account, following function can be used
```sql
azure_storage.account_remove ('storage_account');
```

##### storage_account
Azure storage account contains all of your Azure Storage data objects: blobs, files, queues, and tables. The storage account provides a unique namespace for your Azure Storage data that is accessible from anywhere in the world over HTTP or HTTPS.
 
### Examples
The examples used below makes use of sample Azure storage account `(pgquickstart)` with custom files uploaded for adding to coverage of different use cases. We can start by creating table used across the set of example used.
```sql
CREATE TABLE IF NOT EXISTS public.events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp without time zone
)
```

#### A.	Adding access key of storage account (mandatory for access level = private)
The example illustrates adding of access key for the storage account to get access for querying from a session on the citus cluster.

```sql
SELECT azure_storage.account_add('pgquickstart', 'SECRET_ACCESS_KEY');
```
> [!TIP] `base 64 encoded account key` can be obtained by navigating to Storage account > Access keys 
:::image type="content" source="media/howto-ingestion/azure-blob-storage-account-key.png" alt-text="Screenshot of Security + networking > Access keys section of an Azure Blob Storage page in the Azure portal." border="true":::

#### B.	Removing access key of storage account 
The example illustrates removing the access key for a storage account. This action would result in removing access to files hosted in private bucket in container.

```sql
SELECT azure_storage.account_remove('pgquickstart');
```

#### C.	List the objects within a `public` container on Azure storage account
The example below illustrates accessing the available files within the public container.
```sql
SELECT * FROM azure_storage.blob_list('pgquickstart','publiccontainer');
```

#### D.	List the objects within a `private` container on Azure storage account (adding access key is mandatory)
The example below illustrates accessing the available files within the private container.
```sql
SELECT * FROM azure_storage.blob_list('pgquickstart','privatecontainer');
```

#### E.	List the objects with specific string initials within Azure Storage
The example below illustrates listing all the available files starting with a string initial.

```sql
SELECT * FROM azure_storage.blob_list('pgquickstart','publiccontainer','e');
```
Alternatively

```sql
SELECT * FROM azure_storage.blob_list('pgquickstart','publiccontainer','e') WHERE path LIKE 'e%';
```

#### F.	Read content from file in container
The `blob_get` function retrieves a file from blob storage. In order for blob_get to know how to parse the data you can either pass a value (NULL::table_name), which has same format as the file.

```sql
SELECT * FROM azure_storage.blob_get
        ('pgquickstart'
        ,'publiccontainer'
        ,'events.csv'
        , NULL::events) 
LIMIT 5;
```

Alternatively, we can explicitly define the columns in the `FROM` clause.

```sql
SELECT * FROM azure_storage.blob_get('pgquickstart','publiccontainer','events.csv') 
AS res (
      event_id TEXT,
      event_type DATE,
      event_public INTEGER,
      repo_id INTEGER,
      payload JSONB,
      repo JSONB,
      user_id BIGINT,
      org JSONB,
      created_at TIMESTAMP WITHOUT TIME ZONE)
 LIMIT 5;
```

#### G. Use decoder option
The example illustrates the use of `decoder` option. Normally format is inferred from the extension of the file, but when the file content does not have a matching extension you can pass the decoder argument.

```sql
SELECT * FROM azure_storage.blob_get
        ('pgquickstart'
        ,'publiccontainer'
        ,'events'
        , NULL::events
        , decoder := 'csv') 
LIMIT 5;
```

#### I.	Import filtered content & modify before loading from csv format file
The example illustrates the possibility to filter & modify the content being imported from object in container before loading that into a SQL table.

```sql
SELECT concat('P-',event_id::text) FROM azure_storage.blob_get
        ('pgquickstart'
        ,'publiccontainer'
        ,'events.csv'
        , NULL::events)
WHERE event_type='PushEvent'
LIMIT 5;
```

#### H.	Query content from file with headers, custom separators, escape characters
The example illustrates the use of `options` argument for processing files with headers, custom separators, escape characters, etc., you can either use the COPY command or pass COPY options to the blob_get function using the `azure_storage.options_copy` function.
```sql
SELECT * FROM azure_storage.blob_get
		( 'pgquickstart'
		 ,'publiccontainer'
		 ,'events_pipe.csv'
		 , NULL::events
		 , options := azure_storage.options_csv_get(delimiter := '|' , header := 'true'));
```

#### J.	Aggregation query on content of an object in the container
The example illustrates the ability to directly perform analysis on the data without importing the set into the database. 
```sql
SELECT event_type,COUNT(1) FROM azure_storage.blob_get
		('pgquickstart'
        ,'publiccontainer'
        ,'events.csv'
        , NULL::events)
GROUP BY event_type
ORDER BY 2 DESC
LIMIT 5;
```

### Next Steps

Learn more with utilizing & analysing the dataset alongwith more alternative options
[!INCLUDE][How to ingest data using pg_azure_storage](include/howto-ingest-azure-blob-storage.md.md)
