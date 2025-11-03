# 📦 Outline of Code Files for the Data, ML, & AI Solution Architecture

The following provides an outline to all the Code Files that were created for this Poject. 

**NOTE:** This document does not actually have the Code, it only outlines the Types of Code files that were created for various areas of the Data, ML, & AI Solution Architecture. 

---

##  <br> 🚀 1. Data Ingestion Cloud Functions

### <br> 1.1. Synthetic Data Generation Logic in Main Python File and its Python dependencies File
There were 2 `.txt` format files with Python Code in them.  
<br> **Main Python File Logic:**  
- Defined a lightweight HTTP‑triggered Cloud Function (Gen 2) that generated synthetic telemetry records.  
- Constructed JSON payloads with randomized but schema‑compliant values (timestamps, categorical attributes, numeric sensor readings).  
- Published each JSON payload as a message to a Pub/Sub Topic using the `google-cloud-pubsub` client library.  
- Implemented batching and retry logic to ensure reliable message delivery.  

<br> **Requirements File Logic:**  
- Declared dependencies such as `google-cloud-pubsub`, `numpy`, and `faker` (for synthetic data generation).  
- Ensured the runtime environment had all libraries required for message serialization and Pub/Sub publishing.  

<br> Both these files — the Main Python File and the Requirements File — were packaged into a Cloud Run Function that executed on a schedule, triggered by Cloud Scheduler.

---

### <br> 1.2. Consuming and Loading Messages Logic in Main Python File and its Python dependencies File
There were 2 `.txt` format files with Python Code in them.  
<br> **Main Python File Logic:**  
- Defined a Pub/Sub subscription‑triggered Cloud Function (Gen 2).  
- Consumed messages from the Pub/Sub Subscription, deserialized the JSON payloads, and validated schema compliance.  
- Wrote each message as a JSONL file into the GCS Bronze Layer bucket, partitioned by ingestion timestamp.  
- Implemented error handling with dead‑letter queue support for malformed messages.  

<br> **Requirements File Logic:**  
- Declared dependencies such as `google-cloud-storage` and `google-cloud-pubsub`.  
- Provided libraries for JSON serialization, schema validation, and GCS file writes.  

<br> Both these files — the Main Python File and the Requirements File — were packaged into a Cloud Run Function that executed automatically on message arrival.

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
This Dataform SQLX script File read the cleaned data from the Silver Layer and performed hourly aggregations on key operational metrics per Master Data.

### <br> 2.5. Dataform SQLX script File for Silver -> Exports Gold aggregates to GCS Parquet: 
This Dataform SQLX script File executed the EXPORT DATA command to write the hourly aggregated data from the intermediate BQ Gold table out to the GCS Gold Layer as Parquet files.

### <br> 2.6. Dataform SQLX script File for Silver -> Calculates latest known state per car: 
This Dataform SQLX script File used a window function to identify and store the single, most recent telemetry record for every Master Data, serving the "Current Status" BI visuals.

### <br> 2.7. Dataform SQLX script File for Silver -> Exports Gold latest state to GCS Parquet: 
This Dataform SQLX script File executed the EXPORT DATA command to write the latest state snapshots from the intermediate BQ Gold table out to the GCS Gold Layer as Parquet files.

### <br> 2.8. Dataform SQLX script File for Silver -> Creates Dimension table in Data Warehouse: 
This Dataform SQLX script File created the Dimension table in the BigQuery Data Warehouse to store car Master Data attributes, generating a unique key for use as a foreign key.

### <br> 2.9. Dataform SQLX script File for Silver -> Loads hourly aggregates into Data Warehouse Fact table: 
This Dataform SQLX script File read the hourly aggregated data from the Staging table (which mirrored the Gold output) and loaded it into the final Fact hourly table in the BigQuery Data Warehouse.

### <br> 2.10. Dataform SQLX script File for Silver -> Loads latest state into Data Warehouse Fact table: 
This Dataform SQLX script File read the latest state snapshot data from the Staging table and loaded it into the final Fact latest data table in the BigQuery Data Warehouse.

---

##  <br> 🚀 3. MLOps Training Pipeline & Automation
The Files that contained the logic for running the end-to-end MLOps Pipeline were a combination of a JupyterLab Notebook with Python Code, a Machine Learking Model Training Pythin Code file, a Docker file, a Python dependencies File and the Custom Job Configuration File. 


### <br> 3.1. JupyterLab Notebook with Python Code File for Source Declaration: 
This  




---
