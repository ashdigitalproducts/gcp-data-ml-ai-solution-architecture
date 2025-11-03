# 🏛️ Full Architecture Provisioning Guide
The following cleary describes all the GCP Services and Resources provisoned for this Poject. 

## 🚀 <br> 1. Cloud Project Configuration: 
Setup the GCP Project and set the regional context to the target Region for all subsequent deployments.

---

## 🚀 <br> 2. API Enablement: 
Enable all necessary APIs such as: 
<br>a) **Data Lake Storage** ->	Cloud Storage API	-> `storage.googleapis.com`
<br>b) **Real-Time Orchestration** -> Cloud Functions API -> `cloudfunctions.googleapis.com`
<br>c) **Messaging Queue** -> Cloud Pub/Sub API -> `pubsub.googleapis.com`
<br>d) **Scheduler/Trigger** -> Cloud Scheduler API -> `cloudscheduler.googleapis.com`
<br>e) **Data Warehouse** -> BigQuery API -> `bigquery.googleapis.com`
<br>f) **ELT Orchestration** -> Dataform API -> `dataform.googleapis.com`
<br>g) **BQ External Connections** -> BigQuery Connection API -> `bigqueryconnection.googleapis.com`
<br>h) **BQ Transfers** -> BigQuery Data Transfer API -> `bigquerydatatransfer.googleapis.com`
<br>i) **Unified AI Platform** -> Vertex AI API -> `aiplatform.googleapis.com`
<br>j) **Notebooks/Development** -> Notebooks API -> `notebooks.googleapis.com`
<br>k) **Container Registry** -> Artifact Registry API -> `artifactregistry.googleapis.com`
<br>l) **Underlying Compute** -> Compute Engine API -> `compute.googleapis.com`
<br>m) **Underlying Compute** -> Compute Engine API -> `compute.googleapis.com`
<br>n) **Data Fabric/Catalog** -> Cloud Dataplex API -> `dataplex.googleapis.com`
<br>o) **PII Scanning** -> Sensitive Data Protection (DLP) -> `dlp.googleapis.com`

---

## 🚀 <br> 3. GCP Services and Resources: 
Plan out the GCP Services and Resources which would meet the requirements, the GCP Services and Resources are as follows: 


## ⚙️ <br> 3.1. Service Accounts, Roles and IAM Bindings: 

#### <br> 3.1.1. Ingestion Scheduling: Triggers the start of the data flow.
Roles: 
<br>* roles/run.invoker

#### <br> 3.1.2. Data Generation: Runs the Simulator Function and publishes raw data. 
Roles: 
<br>* roles/pubsub.publisher

#### <br> 3.1.3. Data Loading: Runs the Loader Function and writes files to the Lake.
Roles: 
<br>* roles/storage.objectCreator

#### <br> 3.1.4. ELT Pipeline Orchestration: Orchestrates transformations and moves data through the Medallion Layers.
Roles: 
<br>* roles/storage.objectViewer on GCS Bronze Layer 
<br>* roles/storage.objectAdmin on GCS Silver Layer
<br>* roles/storage.objectAdmin on GCS Gold Layer 

#### 🔄 <br> 3.1.5. Data Transformation: Enables Dataform to run queries and manage tables.
Roles: 
<br>* roles/bigquery.dataViewer on BigQuery Bronze Dataset
<br>* roles/bigquery.dataEditor on BigQuery Staging Dataset
<br>* roles/bigquery.dataEditor on BigQuery Data Warehouse Dataset
<br>* roles/bigquery.jobUser on the Project
<br>* roles/bigquery.connectionUser on the Project

#### <br> 3.1.6. ML Development: Default identity for the Vertex AI Workbench and pipeline execution.
Roles: 
<br>* roles/storage.objectViewer on GCS Silver Layer (to read training data)
<br>* roles/storage.objectAdmin on GCS Gold Layer (for MLOps artifacts)
<br>* roles/storage.objectAdmin on GCS Staging (to stage agent code)
<br>* roles/storage.legacyBucketReader on GCS Staging (to get bucket info)
<br>* roles/aiplatform.user on the Project (to run MLOps jobs)
<br>* roles/bigquery.jobUser on the Project (to run BQ queries from the notebook)
<br>* roles/artifactregistry.admin on the Project (to push Docker images)
<br>* roles/iam.serviceAccountUser on the AI Agent Service Account (to deploy the agent)

#### <br> 3.1.7. ML Prediction: Runs Vertex AI Batch Prediction jobs.
Roles: 
<br>* rainy-days-vertex-predict-sa (The "Prediction Runner")
<br>* roles/aiplatform.user on the Project (to run prediction jobs)
<br>* roles/bigquery.dataEditor on BigQuery Data Warehouse Dataset (to write prediction results)

#### <br> 3.1.8. Governance and DLP: Used by Dataplex for metadata scanning and PII detection.
Roles: 
<br>* rainy-days-dataplex-sa (The "Data Steward")
<br>* roles/storage.objectViewer on GCS Bronze, Silver, and Gold Layers
<br>* roles/dlp.user on the Project (to run PII scans)
<br>* roles/bigquery.metadataViewer on BigQuery Bronze, Staging and Data Warehouse Datasets

#### <br> 3.1.9. Gen AI Agent: Provides the secure identity for the AI Agents to execute their work. 
Roles: 
<br>* roles/bigquery.dataViewer on the BigQuery Data Warehouse Dataset (for the BI Agent tool)
<br>* roles/datacatalog.viewer on the Project (for the Governance Agent tool)
<br>* roles/storage.objectAdmin on GCS Staging  (to read its own staged code)
<br>* roles/iam.serviceAccountUser on itself (to allow Agent Engine to impersonate it)

---

## 🛡️ <br> 3.2. Data Governance Layer: 
One Dataplex Lake and Four Data Zones: Raw, Curated, Product and Analytics. Established a central data fabric for automated metadata cataloging, discovery, and governance. 

---

##  💾 <br> 3.3. Core Data Infrastructure:

### <br> 3.3.1. Google Cloud Storage:
1. Three main Google Cloud Storage Buckets for the Data Lakehouse Medallion Architecture with Bronze Layer, Silver Layer and Gold Layer. 
	<br> a) GCS Bronze Layer: Raw, immutable, ingested JSONL files 
	<br> b) GCS Silver Layer: Cleaned, validated, and partitioned detail records in Parquet files for ML feature engineering
	<br> c) GCS Gold Layer: Final, aggregated, and business-ready data in Parquet files for BI and latest-state reporting and MLOps model output and Vertex AI Pipeline root

2. Two System-Generated Google Cloud Storage Buckets managed by Google Cloud for code deployment and registry: 
	<br> a) Cloud Functions Sources → Bucket used to store the Cloud Functions source code folders
	<br> b) Cloud Functions Uploads → Bucket used to store the temporary build and deployment artifacts (zipped files) created during the deployment process 

3. One Separate Docker Artifact Registry managed service: this has Docker container repository to store the latest image containing the Python code for Model Training


### <br> List of All Folders and File Artifacts in Google Cloud Storage Buckets: 
A comprehensive list of all the Folders and File Artifacts in Google Cloud Storage Buckets and their purpose: 

#### <br> GCS Bronze Layer: 
a) Folder -> Raw Ingested Storage -> Stored the raw, immutable JSONL files as they are ingested from the Cloud Function Loader

#### <br> GCS Silver Layer: 
a) Folder -> Cleaned & Standardized Storage -> Stored the cleaned, standardized, individual record Parquet files and was used as the primary source for ML model training 

#### <br> GCS Gold Layer: 
a) Folder -> Aggregation Storage -> Bucket used to store the aggregated hourly Parquet files
<br> b) Folder -> Last Known State Storage-> Bucket used to store the latest known state Parquet files for each master data
<br> c) Folder -> MLOps Artifacts -> Bucket used to store the model artifacts saved by the Training Job before being registered in the Vertex AI Model Registry
<br> d) Folder -> Vertex AI's staging area -> Bucket used by the Vertex AI pipeline to store intermediate artifacts, logs, and compiled pipeline metadata

#### <br> System-Generated Google Cloud Storage Buckets: 
a) Folder -> Bucket used to store the Cloud Functions source code folders
<br> b) Folder -> Bucket used to store the temporary build and deployment artifacts (zipped files) created during the deployment process 

#### <br> One Separate Docker Artifact Registry managed service: this has Docker container repository to store the latest image containing the Python code for Model Training

---

### <br> 3.3.2. BigQuery: 
  1. Four main BigQuery Datasets:
  a) Bronze Dataset: Housed the External Table providing a persistent SQL interface for querying the raw JSONL files directly from Bronze Layer
	<br> b) Staging Dataset: A temporary holding area making the data ready for final loading and modeling into the Data Warehouse
	<br> c) Data Warehouse Dataset: The actual BigQuery Data Warehouse, serving as the primary source for Looker Studio dashboards and MLOps Output Sink
	<br> d) Dataplex Zone Datasets: automatically generated datasets created by Dataplex when GCS Buckets and BQ Datasets are attached to their respective Dataplex Zones

### <br> List of all BigQuery Tables: 
A comprehensive list of all the BigQuery Tables and their purpose: 

#### Tables in Bronze Dataset: 
a) Table 1: A BigQuery External Table used by Dataform to Read Data directly from the Bronze Layer JSONL files, it did not store any data, but only stored the table's schema and a pointer (URI) to the Bronze Layer files as it provided a persistent SQL query interface over the raw data in Bronze Layer JSONL files

#### Tables in Staging Dataset: 
a) Table 1: A Temporary table used by Dataform during ELT to hold the cleaned, standardized, and de-duplicated individual records before exporting to GCS Silver Layer
<br> b) Table 2: A Temporary table used by Dataform to hold the hourly aggregated metrics before exporting to GCS Gold Layer 
<br> c) Table 3: A Temporary table used by Dataform to hold the single latest known records 

#### Tables in Data Warehouse Dataset: 
a) Table 1: A Dimension Table storing static or slowly changing attributes for master data
<br> b) Table 2: A Fact Table storing the final, business-ready hourly aggregated metrics and Primary source for BI trend analysis
<br> c) Table 3: A Fact Table storing the final latest known state for each master data and Primary source for "Current Status" BI visuals
<br> d) Table 4: A Table that Stores outputs from the MLOps Output = Classification Model
<br> e) Table 5: A Table that Stores outputs from the MLOps Output = Clustering Model
<br> f) Table 6: A Table that Stores outputs from the MLOps Output = Regression Model
<br> g) Table 7: A Table that Stores outputs from the MLOps Output = Time Series Forecasting Model

---

## 📥 <br> 3.4. Real-Time Data Ingestion:
A. One Cloud Scheduler Job to trigger the Data Ingestion Automation flow at regular intervals. 
<br>B. One Cloud Function (Gen 2) that Simulates source data at regular intervals. Cloud Function is triggered by the Cloud Scheduler. 
<br>C. One Pub/Sub Topic that Receives JSON messages from the Simulator Cloud Function. 
<br>D. One Pub/Sub Subscription that Pushed all the messages to Loader Cloud Function. 
<br>E. One Cloud Function (Gen 2) that Gets the messages from Pub/Sub Subscription and Loads the data to the GCS Bucket - Bronze Layer.  
<br>F. One Cloud Scheduler Job to trigger Dataform Script Automation 30 minutes after new data lands in GCS Bronze Layer. 
<br>G. One Cloud Function (Gen 2) that is triggered by the Cloud Scheduler Job and performs the Dataform Script Automation. 
<br>H. Four Dataform Scripts that perform ETL at various Stages such as: 
	<br>* 1) Between Bronze Layer and Silver Layer: Cleaning & Standardization
	<br>* 2) Between Silver Layer and Gold Layer: Aggregation & State Tracking
	<br>* 3) Between Gold Layer and Staging area: Data Loading
	<br>* 4) Between Staging area and Data Warehouse: Final Modeling & Data Warehouse Load

---

## 🎯 <br> 3.5. MLOps Layer:
A. One Cloud Scheduler Job to trigger MLOps Pipeline Automation of Batch Prediction and Scoring. 
<br>B. One Cloud Function (Gen 2) that is triggered by the Cloud Scheduler Job and performs the MLOps Pipeline Automation of Batch Prediction and Scoring. 
<br>C. One Vertex AI Workbench Instance with One JupyterLab Notebook. 
<br>D. One Containerized Python code for Model Training. 
<br>E. One Vertex AI Model Registry where the trained Model artifact was versioned and stored after the training job was completed. 
<br>F. One Vertex AI Batch Prediction Job that was scheduled to run on regular intervals to consume the trained Model artifact and generate Batch Predictions and store the Batch Prediction in Data Warehouse Dataset. 

---

## 📊 <br> 3.6. Business Intelligence Layer: 
One Looker Studio Dashboard to display pre-MLOps data visualizations such as trends and current status and post-MLOps data visualizations such as predictions, classifications and risk scores

---

## 🤖 <br> 3.7. AI Agent Layer: 

Two AI Agents built using Vertex AI Studio UI: 

#### <br>1. AI Agent 1: The BI Analyst Agent
This agent transforms natural language business questions into actionable SQL queries against the structured Data Warehouse.
Allows business users to query the Data Warehouse using natural language. 


#### <br>2. AI Agent 2: The Data Governance Agent
This agent answers questions about data compliance and structure by querying the centralized metadata catalog.
Answer questions regarding metadata, security, and lineage. 
<br>For ex: 
<br> a) "Where is the raw telemetry data stored?" 
<br> b) "Which tables contain PII?"
<br> c) "What are the columns in the Silver layer?"

---
  
