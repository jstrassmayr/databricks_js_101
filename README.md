# databricks_js_101
This repo contains code and docs for starting out with Databricks.

# Databricks Workspace Overview

## Open Workspace
 - Go to https://portal.azure.com
 - Search for Databricks
 - Select Playground Workspace
 - "Launch Workspace"

## Take a look around
Sidebar
 - Workspace: View and manage your code here
 - Catalog: Browse and manage your data (tables, schema, catalogs AND volumes, folders, files)
 - SQL Editor: Develop your CRUD (CREATE, READ, UPDATE, DELETE) operations
 - Compute and SQL Warehouses: This is the hardware that runs your queries, python scripts, jobs and pipelines.
 - Data Ingestion: Start to ingest data by uploading data or use existing connectors for all kinds of datasources.
 - Delta Live Tables: Build data pipelines and Databricks will recognize and handle script dependencies automatically for execution.

![image](https://github.com/user-attachments/assets/a423c17a-8965-4d79-b337-043b07d28aab)



# File upload to managed Storage

## 1: Upload and ingest to table
-> New -> Data 
- „Create or modify table“
- Choose a file (e.g. [baby-names.csv](https://github.com/hadley/data-baby-names/blob/master/baby-names.csv))
- Choose a Catalog (e.g. "dbx_101_js")
- Choose a Schema (e.g. "bronze")
- Choose a Table name (with custom suffix e.g. your initials)
- -> Create table

The file will then be ingested and its data will be stored in a new table. The file and table are not linked.
You can browse the new table in (your) Catalog.

## 2: Upload and query file
-> New -> Data
- „Upload files to volume“
- Expand a Catalog (e.g. "dbx_101_js")
- Expand a Schema (e.g. "bronze")
- Choose (or create) a Volume (e.g. "manual_uploads")
- Choose a file (e.g. [baby-names.csv](https://github.com/hadley/data-baby-names/blob/master/baby-names.csv))
- -> Upload

The file will then be uploaded and can be viewed in (your) Catalog.


# Query uploaded data
Open the "SQL Editor" and create a new query (using the + button). 
Within the editor type "LIST '<path>'" and paste the "volume file path" which you can copy from the csv-file's dropdown menu or its parent folder.
Hit CTRL+Enter to execute the command.

```
>> LIST '/Volumes/<<catalog>>/<<schema>>/<<volume>>'
```
(!) Use Single-Quotes (') in this statement.

![image](https://github.com/user-attachments/assets/bb130e87-b7ba-4423-a3e4-62e5dc84918f)


To query the file use a SELECT statement.
```
>> SELECT * FROM csv.`/Volumes/<<catalog>>/<<schema>>/<<volume>>/<<filename.csv>>`
```
(!) Use Backticks (`) in this statement.

Do you notice anything about the column headers?

## Query data using 'read_files()'
We can use the [read_files()](https://learn.microsoft.com/en-us/azure/databricks/sql/language-manual/functions/read_files) function with parameters to read JSON, CSV, XML, TEXT, BINARYFILE, PARQUET, AVRO, and ORC files.

```
SELECT * FROM read_files(
  '/Volumes/dbx_101_js/bronze/manual_uploads/baby-names.csv',
  format => 'csv',
  header => true)
```
![image](https://github.com/user-attachments/assets/09e08c32-8a40-43aa-ad1b-cf6941881355)

Notice the _rescued_data column? See the [docs](https://docs.databricks.com/en/ingestion/cloud-object-storage/auto-loader/schema.html#what-is-the-rescued-data-column). use "schemaEvolutionMode => 'none'" to hide it.

