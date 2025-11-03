# 📦 Outline of Code Files for the Data, ML, & AI Solution Architecture

The following provides an outline to all the Code Files that were created for this Poject. 

**NOTE:** This document does not actually have the Code, it only outlines the Types of Code files that were created for various areas of the Data, ML, & AI Solution Architecture. 

---

##  <br> 🚀 1. Data Ingestion Cloud Functions

### <br> 1. Synthetic Data Generation Logic in Main Python File and its Python dependencies File: 
<br> There were 2 .txt format files with Python Code in them. 
<br> The Main Python File contained the code to generate Synthetic Data and publish Event Messages to the Pub/Sub Topic. 
<br> The Requirements File contained the Python dependencies to support the Main Python File. 
<br> Both these files: Main Python File and the Requirements File were part of the Cloud Run Function that ran this Code. 

### <br> 2. Consuming and Loading Messages Logic in Main Python File and its Python dependencies File: 
<br> There were 2 .txt format files with Python Code in them. 
<br> The Main Python File contained the code to consume the Messages from Pub/Sub Subscription and write JSON files to the Cloud Storage Bucket - Bronze Layer. 
<br> The Requirements File contained the Python dependencies to support the Main Python File. 
<br> Both these files: Main Python File and the Requirements File were part of the Cloud Run Function that ran this Code. 

---

##  <br> 🚀 2. Dataform ELT Pipeline

There were 10 Dataform SQLX script Files with .sqlx extension that defined the data transformations within the Medallion Architecture. 
There was 1 Project configuration Json File that provided Project configuration, default schema, database, and variables. 

### <br> 1. Dataform SQLX script File for Source Declaration: 
<br> This first Dataform SQLX script File declared the External Table as the Source. 

### <br> 2. Consuming and Loading Messages Logic in Main Python File and its Python dependencies File: 
<br> There were 2 .txt format files with Python Code in them. 
<br> The Main Python File contained the code to consume the Messages from Pub/Sub Subscription and write JSON files to the Cloud Storage Bucket - Bronze Layer. 
<br> The Requirements File contained the Python dependencies to support the Main Python File. 
<br> Both these files: Main Python File and the Requirements File were part of the Cloud Run Function that ran this Code. 

---

##  <br> 🚀 3. MLOps Training Pipeline & Automation

---
