# databricks_js_101
This repo contains code and docs for starting out with Databricks.

# Databricks Workspace Overview

## Open Workspace
 - Go to https://portal.azure.com
 - Search for Databricks
 - Select Playground Workspace
 - "Launch Workspace"

## Take a look around
 - Workspace: View and manage your code here
 - Catalog: Browse and manage your data (tables, schema, catalogs AND volumes, folders, files)
 - SQL Editor: Develop your CRUD (CREATE, READ, UPDATE, DELETE) operations
 - SQL Warehouses: SQL warehouse is a compute resource (actual hardware) that runs your queries on Azure Databricks.

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
