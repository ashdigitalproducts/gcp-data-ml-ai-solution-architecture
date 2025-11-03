# 🚀 Techn Stack  

This section details the core technologies, **Google Cloud services**, and supporting infrastructure used to build and operate this MLOps platform, from data ingestion and transformation to model training and deployment.

---

## 🛠️ Core Tech Stack & Languages

A modern approach with cloud-native stack, leveraging **Python** for machine learning and automation, and **SQL** for robust data pipelines.

---

## ☁️ Google Cloud Services (GCP)

Google Cloud forms the backbone of the platform, providing scalable and managed services for every step of the **MLOps lifecycle**.

### Data Storage & Messaging
* **Cloud Storage (GCS):** The central data lake, implementing the **Medallion Architecture** (Bronze, Silver, Gold layers) for reliable data processing. Also used for staging and model artifacts.
* **Pub/Sub:** Serves as the **event messaging backbone** for reliable, asynchronous data ingestion.

### Compute & Orchestration
* **Cloud Functions (Gen 2):** Used for lightweight, serverless tasks, including the **data generator**, data loader, and orchestration triggers.
* **Cloud Scheduler:** Provides **timed triggers** to initiate ingestion, ELT, and MLOps pipelines on a recurring basis.

### Data Warehousing & Transformation
* **BigQuery:** The scalable, fully-managed **Data Warehouse**. Used for external tables, staging, dimension/fact tables, and storing final ML outputs.
* **Dataform:** Orchestrates the **SQLX-based ELT** (Extract, Load, Transform) pipelines across the Bronze, Silver, and Gold layers of the Medallion Architecture.

### Machine Learning (Vertex AI Suite)
* **Vertex AI Workbench:** Provides **JupyterLab notebooks** for interactive data analysis, prototyping, and experimentation.
* **Vertex AI Model Registry:** Manages **model versioning**, metadata, and artifact storage.
* **Vertex AI Batch Prediction:** Executes **scheduled scoring jobs**, writing prediction results directly into BigQuery.
* **Artifact Registry:** Stores all required **container images** for consistent and reproducible training jobs.
* **Vertex AI Studio Agents:** Currently in prototype phase for a **BI Analyst Agent** and a **Governance Agent**.

### Governance & Reporting
* **Dataplex:** Provides **data governance**, unified metadata cataloging, and essential services like **PII scanning**.
* **Looker Studio:** Used for **BI dashboards** to visualize trends, current operational state, and monitor ML predictions.

---

## 🧩 MLOps Libraries & Frameworks

We rely on established Python libraries for both **Core ML/Data Science** and seamless **Cloud Integration**.

### Core ML & Data Science
* **scikit-learn:** Used for **training the baseline `RandomForestClassifier`**, essential evaluation metrics (accuracy, ROC, confusion matrix), and feature preprocessing.
* **pandas:** The workhorse for **data wrangling**, schema alignment, and preparing features for training and prediction.
* **numpy:** Core library for numerical operations, array manipulation, and advanced **feature engineering** (e.g., rolling averages, lag features).
* **joblib:** Handles **model serialization and deserialization** (saving trained models as artifacts and loading them for prediction).

### Cloud Integration & Automation
* **google-cloud-aiplatform:** The primary **Vertex AI SDK** for programmatically managing the MLOps workflow: submitting Custom Training Jobs, registering models, and launching Batch Prediction Jobs.
* **gcsfs:** Enables **direct read/write** operations between pandas DataFrames and Google Cloud Storage (e.g., loading Parquet from Silver/Gold layers).
* **pandas-gbq:** Facilitates writing pandas DataFrames **directly into BigQuery tables** (e.g., `fact_predictions_car_failure`).
* **faker & uuid:** Used together for **generating synthetic telemetry data** and creating unique prediction IDs for traceability during simulation.

### Supporting Infrastructure (Planned Enhancements)
* **IAM Service Accounts:** Implemented with **fine-grained roles** to ensure secure, least-privilege access across ingestion, ELT, ML, and governance components.
* **Cloud Logging & Monitoring (planned):** Future focus on robust **observability**, centralized error reporting, and automated alerting.
* **Lifecycle Management Policies (planned):** Will be configured for **automated cleanup** and cost management of system-managed GCS buckets.

---
