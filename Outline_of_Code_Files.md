# 📦 Outline of Code Files for the Data, ML, & AI Solution Architecture

The following provides an outline to all the Code Files that were created for this Poject. 

**NOTE:** This document does not actually have the Code, it only outlines the Types of Code files that were created for various areas of the Data, ML, & AI Solution Architecture. 

---

##  <br> 🚀 1. Data Ingestion Cloud Functions

### <br> 1.1. Synthetic Data Generation Logic in Main Python File and its Python dependencies File: 
There were 2 .txt format files with Python Code in them. 
<br> The Main Python File contained the code to generate Synthetic Data and publish Event Messages to the Pub/Sub Topic. 
<br> The Requirements File contained the Python dependencies to support the Main Python File. 
<br> Both these files: Main Python File and the Requirements File were part of the Cloud Run Function that ran this Code. 

### <br> 1.2. Consuming and Loading Messages Logic in Main Python File and its Python dependencies File: 
There were 2 .txt format files with Python Code in them. 
<br> The Main Python File contained the code to consume the Messages from Pub/Sub Subscription and write JSON files to the GCS Bronze Layer Bucket. 
<br> The Requirements File contained the Python dependencies to support the Main Python File. 
<br> Both these files: Main Python File and the Requirements File were part of the Cloud Run Function that ran this Code. 

---

##  <br> 🚀 2. Dataform ELT Pipeline
There were 10 Dataform SQLX script Files with .sqlx extension that defined the data transformations within the Medallion Architecture. 
There was 1 Project configuration Json File that provided Project configuration, default schema, database, and variables. 


### <br> 2.1. Dataform SQLX script File for Source Declaration: 
This first Dataform SQLX script File declared the External Table as the Source. 
 
### <br> 2.2. Dataform SQLX script File for Bronze -> Silver cleanup and standardization: 
This Dataform SQLX script File defined the Bronze-to-Silver transformation step. 
<br> Its primary function was to read the raw, unvalidated data from GCS Bronze Layer Bucket's entry point and apply crucial cleaning, standardization, and type casting rules before making the data available for analytics and machine learning.

### <br> 2.3. Dataform SQLX script File for Bronze -> Exports cleaned Silver table to GCS Parquet: 
This Dataform SQLX script File executed the EXPORT DATA command, taking the data from that newly created internal BQ table and write it out to the GCS Silver Layer Bucket as Parquet files.

### <br> 2.4. Dataform SQLX script File for Silver -> Gold hourly aggregation: 
This Dataform SQLX script File 

### <br> 2.5. Dataform SQLX script File for Silver -> Exports Gold aggregates to GCS Parquet: 
This Dataform SQLX script File 

### <br> 2.6. Dataform SQLX script File for Silver -> Calculates latest known state per car: 
This Dataform SQLX script File 

### <br> 2.7. Dataform SQLX script File for Silver -> Exports Gold latest state to GCS Parquet: 
This Dataform SQLX script File 

### <br> 2.8. Dataform SQLX script File for Silver -> Creates Dimension table in Data Warehouse: 
This Dataform SQLX script File 

### <br> 2.9. Dataform SQLX script File for Silver -> Loads hourly aggregates into Data Warehouse Fact table: 
This Dataform SQLX script File 

### <br> 2.10. Dataform SQLX script File for Silver -> Loads latest state into Data Warehouse Fact table: 
This Dataform SQLX script File 


---

##  <br> 🚀 3. MLOps Training Pipeline & Automation

---
