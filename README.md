# databricks_js_101
This repo contains code and docs for starting out with Databricks.

# Databricks Workspace Overview

## Open Workspace
 - Go to https://portal.azure.com
 - Search for Databricks
 - Select Playground Workspace
 - "Launch Workspace"

## Take a look around

The most relevant items in the side bar are:
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
```sql
>> SELECT * FROM csv.`/Volumes/<<catalog>>/<<schema>>/<<volume>>/<<filename.csv>>`
```
(!) Use Backticks (`) in this statement.

Do you notice anything about the column headers?

## Query data using 'read_files()'
We can use the [read_files()](https://learn.microsoft.com/en-us/azure/databricks/sql/language-manual/functions/read_files) function with parameters to read JSON, CSV, XML, TEXT, BINARYFILE, PARQUET, AVRO, and ORC files.

```sql
SELECT * FROM read_files(
  '/Volumes/dbx_101_js/bronze/manual_uploads/baby-names.csv',
  format => 'csv',
  header => true)
```
![image](https://github.com/user-attachments/assets/09e08c32-8a40-43aa-ad1b-cf6941881355)

Notice the _rescued_data column? Add the option "schemaEvolutionMode => 'none'" to hide it. See the [docs](https://docs.databricks.com/en/ingestion/cloud-object-storage/auto-loader/schema.html#what-is-the-rescued-data-column) for more info.


# Pipelines
Let's create a Python Notebook to read data from Bronze layer, modify it and write it to Silver layer.
 - -> New -> Notebook
 - Choose a name (e.g. "Bronze to Silver")
   
## Silver layer: Read and display filtered data
When using Python in data engineering, you will use "Dataframe"s. Dataframe objects contain tabular data and are very usefull to modify, filter, rename, aggregate etc. There are 2 types of dataframes widely used: [Pandas](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html)- and [Spark-Dataframes](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/api/pyspark.sql.DataFrame.html). Both serve similar purposes but Spark DFs were designed for Big Data handling. You can convert each one's data back and forth as needed.

Copy the following Python code and paste it into the first cell of the notebook. Then hit -> "Run cell".
```python
babynames = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/Volumes/dbx_101_js/bronze/manual_uploads/baby-names.csv")
babynames.createOrReplaceTempView("babynames_table")
years = spark.sql("select distinct(Year) from babynames_table").toPandas()['Year'].tolist()
years.sort()
dbutils.widgets.dropdown("Year", "2008", [str(x) for x in years])
display(babynames.filter(babynames.year == dbutils.widgets.get("Year")))
```

This will result in a similar view as this:
![image](https://github.com/user-attachments/assets/8b214dd8-b906-45fc-8f0b-59386da486bd)

## Write to Silver
 - Create a new code cell (by hovering your mouse below the current cell) and click "+ Code"
 - Insert the following code
 - Modify the code accordingly to write to the correct catalog
   
```python
babynames.write.format("delta") \
    .mode("overwrite") \
    .saveAsTable("<<catalog>>.silver.babynames_<<your_suffix>>")
```
This will (over)write the content of the dataframe to a table.

## Write to Gold: Top baby names
Let's create a Python Notebook to read data from Silver layer, find the top boy and girl name per year and write them to Gold layer.
 - -> New -> Notebook
 - Change the notebook title (e.g. "Silver to Gold")
 - Change the first code cell's type to 'SQL'
 - Click on the word "generate" to get some AI help 
 - ![image](https://github.com/user-attachments/assets/7c8a8c3e-84d3-4207-93aa-7af436a03d75)
 - Enter a prompt similar to 'retrieve the most used (percent) girl and boy names per year from the table babynames'
 - This should result in this
 - ```sql
   %sql
   SELECT year, sex, name, percent AS max_percent
   FROM (
     SELECT year, sex, name, percent,
            RANK() OVER(PARTITION BY year, sex ORDER BY percent DESC) AS rank
     FROM dbx_101_js.silver.babynames_js
   ) ranked
   WHERE ranked.rank = 1
   ORDER BY year, sex;
   ```

A SELECT statement's result is stored in a dataframe named "_sqldf" and can be used in other Python and SQL cells. Let's write the result to a table top_babynames:
 - Add a new Python code cell
 - Insert the following code
 - ```python
   _sqldf.write.mode("overwrite").saveAsTable("top_babynames_<<suffix>>")
   ```

## Set up the workflow
Now, let's create a Pipeline (aka. Workflow) which consecutively executes the load of Silver and Gold.
 - Click "Workflows" in the sidebar
 - Click on "Create Job"
 - Add a task for the Silver layer load
   - Task name: Silver
   - Type: Notebook
   - Source: Workspace
   - Path: <<Choose the previously created notebook 'Bronze to Silver'>>
   - -> Create task
 - Add a task for the Gold layer load
   - Do the same as above but choose the "Silver to Gold" notebook
 - Choose a proper job name (e.g. "Full medallion load")
 - -> Run now (takes 1-2 minutes)

You have now loaded and aggregated data from Bronze to Gold using several techniques such as Python and SQL.

## Scheduling jobs
Let's add a trigger to our pipeline so we can run it on a schedule.
 - Click "Workflows" in the sidebar
 - Open the job you just created. You are now on the "Runs" overview which shows when the pipeline has been running and how it ended (success or failure). To see the tasks ("Silver" and "Gold") you created previously, open the "Tasks" overview (top left).
 - On either of these 2 pages you can see Job details on the right. Click -> Add trigger
 - Choose a periodicity and -> Save
 - ![image](https://github.com/user-attachments/assets/6b11826a-0c2f-42e4-9e40-85027aa5288b)

