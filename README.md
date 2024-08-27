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
Within the editor type "List '" and activate Autofill by hitting CTRL+Space. This should help you fill the command with that syntax.

```
>> LIST '/Volumes/<<catalog>>/<<schema>>/<<volume>>'
```
(!) Use Single-Quotes (') in this statement.



```
>> select * from csv.`/Volumes/<<catalog>>/<<schema>>/<<volume>>/<<filename.csv>>`
```
(!) Achtung: Es müssen Backticks (`) verwendet werden und nicht Single-Quotes (').
