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
There were Dataform SQLX script Files with .sqlx extension that defined the data transformations within the Medallion Architecture. 
There was 1 Project configuration Json File that provided Project configuration, default schema, database, and variables. 

---

##  <br> 🚀 3. MLOps Training Pipeline & Automation
The Files that contained the logic for running the end-to-end MLOps Pipeline were a combination of a JupyterLab Notebook with Python Code, a Machine Learning Model Training Python Code file, a Docker file, a Python dependencies File and the Custom Job Configuration File.

### <br> 3.1. JupyterLab Notebook: rainy_days_ml_prototype.ipynb
This interactive Notebook was the development lab for prototyping the end‑to‑end ML workflow.  
<br> **Logic in the Notebook Cells:**  
- Connected to BigQuery and GCS to pull curated telemetry data.  
- Performed exploratory data analysis (EDA) to inspect schema, distributions, and missing values.  
- Engineered prototype features (rolling averages, categorical encodings, lag features).  
- Trained a `RandomForestClassifier` on a sample dataset and evaluated performance with accuracy, ROC, and confusion matrix.  
- Submitted a one‑off Batch Prediction job using the Vertex AI Python SDK (`aiplatform.BatchPredictionJob.create`) to validate integration with BigQuery.  
- Served as the scratchpad for experimentation before productionizing the logic into Model Training.

---

### <br> 3.2. Training Code Blueprint: train.py
This Python script was the production‑ready training entrypoint.  
<br> **Logic in the Script:**  
- Parsed command‑line arguments for input paths, output paths, and project/region parameters.  
- Loaded cleaned Parquet files from the Silver Layer (via GCSFS or BigQuery).  
- Applied deterministic feature engineering: type casting, scaling, encoding, and feature selection.  
- Trained a `RandomForestClassifier` with fixed random seeds for reproducibility.  

---

### <br> 3.3. Container Instructions: Dockerfile
This file defined the container environment for the training job.  
<br> **Logic in the Dockerfile:**  
- Started from a lightweight Python base image (e.g., `python:3.9-slim`).  
- Copied `requirements.txt` into the image and installed dependencies with `pip install -r requirements.txt`.  
- Copied `train.py` into the working directory.  
- Set the container entrypoint to Training Model so Vertex AI Custom Jobs could pass runtime arguments.  
- Guaranteed reproducibility and portability of the training environment.

---

### <br> 3.4. Dependency List: requirements.txt
This file specified all Python dependencies required for training and integration.  
<br> **Logic in the File:**  
- `scikit-learn` for the RandomForestClassifier.  
- `pandas` and `numpy` for data manipulation and numerical operations.  
- `gcsfs` for reading/writing directly to GCS.  
- `google-cloud-aiplatform` for model registration and job submission.  
- `joblib` for model serialization.  
- Ensured consistent, pinned versions to avoid runtime incompatibilities.

---

### <br> 3.5. Custom Job Configuration: config.yaml
This YAML file defined the runtime configuration for launching the training job on Vertex AI.  
<br> **Logic in the File:**  
- Declared job metadata: job name, display name, and labels.  
- Specified machine type (`n1-standard-4`) for CPU‑based training.  
- Referenced the container image URI in Artifact Registry built from the Dockerfile.  
- Defined input paths (Silver Layer or BigQuery dataset) and output paths (Gold Layer for artifacts).  
- Bound the runtime Service Account with `aiplatform.user` and `storage.objectAdmin` roles.  
- Provided Vertex AI with all parameters to execute `train.py` inside the container.

---

### <br> 3.6. End-to-End Flow
- **Notebook** → Feature discovery, prototyping, and validation.  
- **Training Script** → Productionized training logic with reproducible feature engineering and model saving.  
- **Dockerfile + Requirements** → Packaged the training environment into a container image stored in Artifact Registry.  
- **Configuration file** → Defined the execution environment and launched the container as a Vertex AI Custom Job.  
- **Outputs** → Model artifact saved to the Gold Layer → Registered in Vertex AI Model Registry → Consumed by Batch Prediction jobs → Predictions written back into BigQuery for BI dashboards.

---

##  <br> 🚀 3. Automation Glue Logic (Serverless Orchestration)
This section details the critical glue code that eliminated manual intervention from the pipeline by programmatically chaining the Ingestion flow, the Dataform ELT process, and the Vertex AI MLOps scoring job using Cloud Scheduler and dedicated Cloud Functions. This design ensures the entire end-to-end data flow is self-triggering, reliable, and decoupled.

The automation relies on two main Python scripts packaged into distinct, authenticated Cloud Functions (Gen 2):

### <br> 4.1. ELT Automation Runner:
This function is responsible for ensuring the transformed data is ready in the Data Warehouse shortly after new raw data lands in the Bronze Layer.

- Trigger: Launched by the dedicated ELT Cloud Scheduler Job, which is set to run automatically 30 minutes after the Ingestion Scheduler Job.

- Identity: Runs as the Dataform ELT Service Account, which possesses the necessary permissions (roles/dataform.editor) to interact with the Dataform API.

- Logic in the Script:
- - Utilizes the google-cloud-dataform client library.
- - The Python code programmatically constructs a request to the Dataform API, targeting the latest code within the defined Dataform workspace.
- - It calls the client.create_workflow_invocation method to trigger an asynchronous run, launching the entire 10-script ELT pipeline—from reading the Bronze External Table through to loading the final Fact and Dimension tables in the BigQuery Data Warehouse.

- Purpose: This entirely eliminates the manual execution step of the Dataform pipeline, ensuring immediate data transformation readiness once the raw files are present.


### <br> 4.2. MLOps Batch Scorer:  
This function is responsible for launching the daily predictive scoring run using the trained model, completing the MLOps automation loop.

- Trigger: Launched by the dedicated MLOps Cloud Scheduler Job, which is scheduled to run daily (e.g., 1 AM UTC).

- Identity: Runs as the ML Prediction Service Account, which has the roles/aiplatform.user role to launch Vertex AI jobs and the roles/bigquery.dataEditor role to write results to the DWH.

- Logic in the Script:
- - Uses the google-cloud-aiplatform SDK.
- - The script first queries the Vertex AI Model Registry to find the latest version of the registered car failure model.
- - It then defines and submits a Vertex AI Batch Prediction Job by calling aiplatform.BatchPredictionJob.create.
- - The job is configured to use the fact table in the BigQuery DWH as its input source and the DWH dataset as its output sink.

- Purpose: This fully automates the daily prediction and scoring process, ensuring the Looker Studio dashboards always display fresh, ML-generated risk scores without any human intervention.

---
