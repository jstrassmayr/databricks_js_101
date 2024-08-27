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

## 1: Upload and Ingest
-> Neu
- „Create or modify table“
- Datei auswählen/hochladen
- Neuen Catalog/Schema erstellen
- Neue Tabelle erstellen
- Advanced: Separator auswählen

==> Die Datei wird eingelesen und steht als Tabelle zur Verfügung!


## 2: Upload to volume
-> Neu
- „Upload file to volume“
- Choose (or create) a Volume
- Datei auswählen/hochladen

==> Die Datei wird nur hochgeladen und steht im Volume als Datei zur Verfügung!

```
>> LIST '/Volumes/<<catalog>>/<<schema>>/<<volume>>'
```
(!) Achtung: Es müssen Single-Quotes (') verwendet werden.



# Abfragen der hochgeladenen Bronze-Daten via SQL
```
>> select * from csv.`/Volumes/<<catalog>>/<<schema>>/<<volume>>/<<filename.csv>>`
```
(!) Achtung: Es müssen Backticks (`) verwendet werden und nicht Single-Quotes (').
