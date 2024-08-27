# databricks_js_101
This repo contains code and docs for starting out with Databricks.

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
>> LIST '/Volumes/<<catalog>>/<<schema>>/<<schema>>'
```

(!) Achtung: Es müssen Single-Quotes (') verwendet werden.
