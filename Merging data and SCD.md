# MERGE INTO command

You can upsert data from a source table, view, or DataFrame into a target Delta table by using the MERGE SQL operation. 
Delta Lake supports inserts, updates, and deletes in MERGE, and it supports extended syntax beyond the SQL standards to facilitate advanced use cases.

## Syntax
```
MERGE INTO target
USING source
ON source.key = target.key
WHEN MATCHED THEN
  UPDATE SET *
WHEN NOT MATCHED THEN
  INSERT *
WHEN NOT MATCHED BY SOURCE THEN
  DELETE
```

# SCD Type 2
https://www.youtube.com/watch?v=GhBlup-8JbE

# Sources
- https://docs.databricks.com/en/delta/merge.html