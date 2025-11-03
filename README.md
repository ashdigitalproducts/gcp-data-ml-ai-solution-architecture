# 🌟 Comprehensive GCP Data, ML, & AI Solution Architecture

This repository outlines the blueprint for a **serverless, end-to-end Data, ML, & AI Solution Architecture** built on Google Cloud Platform. 
It showcases expertise across Data Ingestion, tiered ELT, Medallion Architecture, Data Warehousing, Automated Machine Learning, Data Governance and Gen AI Integration.

This Solution Architecture is focused towards ingesting Real-time Streamed Events Data that comes in the form of Json semi-structured data. 

**NOTE:** This repository is only a descriptive write-up of the actual Design and Implementation process followed by the Architect. This repository is not intended to provide readers ability to fork, clone or download anything.  

---

## 💡Goals

1. Goal 1: Build a Comprehensive Data, ML, & AI Solution Architecture, designed and implemented fully on GCP.
2. Goal 2: Plan the Architecture in such a way that it utilizes appropriate GCP Services and Resources to meet the requirements.
   
---

## 🚀 Architectural Scope

The Platform processes simulated real-time telemetry data, transforming raw JSON into trusted DWH fact tables for BI and predictive scoring.

| Skill Area | Key GCP Services Demonstrated |
| :--- | :--- |
| **Real-Time Ingestion** | Cloud Scheduler, Cloud Functions (Gen 2), Pub/Sub |
| **Storage, ELT & Modeling** | GCS Medallion Layers: Bronze, Silver, Gold, BigQuery, Dataform |
| **MLOps & Prediction** | Vertex AI Workbench, Vertex AI Model Registry, Batch Prediction |
| **Governance & AI** | Dataplex, Vertex AI Agents |
| **BI & Reporting** | Data Visualization using Looker Studio Dashboard |

---

## 🗺️ Solution Architecture Diagram

<img src="https://github.com/user-attachments/assets/694dc66c-2561-49ea-b97f-d728d92a6edc" alt="GCP Data and AI Solution Architecture Diagram" width="100%">

*(The Solution Architecture Diagram shows the end-to-end flow: starting from the Data Sources till the Data Visualization.)*

---

## 🏛️ Architecture Provisioning Details (High-Level Design and Architecture)

## Infrastructure Blueprint
The following table lists out the main infrastructure area where the GCP Services and Resources were provisoned and their purpose. 

| Infra Area | GCP Services & Resources | Purpose |
| :--- | :--- | :--- |
| **Cloud Project Configuration** | GCP Project and Region | Setup the GCP Project and set the regional context to the target Region for all subsequent deployments. |
| **API Enablement** | **Data Ingestion & Storage:**<br>* `cloudfunctions.googleapis.com`<br>* `pubsub.googleapis.com`<br>* `cloudscheduler.googleapis.com`<br><br>**ELT, Modeling, & Data Warehouse:**<br>* `bigquery.googleapis.com`<br>* `dataform.googleapis.com`<br>* `storage.googleapis.com`<br><br>**MLOps, Training, & AI:**<br>* `aiplatform.googleapis.com` <br>* `notebooks.googleapis.com` <br>* `artifactregistry.googleapis.com`<br><br>**Data Governance:**<br>* `dataplex.googleapis.com`<br>* `dlp.googleapis.com`<br><br>**Infrastructure:**<br>* `compute.googleapis.com`<br>* `cloudbuild.googleapis.com`<br>* `run.googleapis.com` | Enable all necessary APIs—including Compute, Cloud Functions, Pub/Sub, Cloud Scheduler, BigQuery, Dataform, Vertex AI and Dataplex. |
| **Data Governance Layer** | Dataplex Lake and Four Data Zones: Raw, Curated, Product and Analytics. | Establish a central data fabric for automated metadata cataloging, discovery and governance. |
| **Core Data Infrastructure** | Google Cloud Storage Buckets and BigQuery Datasets | Data Lakehouse Medallion Architecture with Bronze Layer, Silver Layer and Gold Layer, with a dedicated Data Warehouse that houses External Table, Dimensional Tables, MLOps Output Tables and Dataplex attached Tables. |
| **Real-Time Data Ingestion** | Cloud Scheduler Job, Cloud Function (Gen 2), Pub/Sub Topic & Subscription and Dataform Scripts | Orchestrates the full data collection flow, from simulated event generation and secure publishing (Pub/Sub) to atomic loading of raw telemetry files into the Bronze Layer. |
| **MLOps Pipeline** | Vertex AI Workbench Instance, JupyterLab Notebook, Containerized Python code for Model Training, Vertex AI Model Registry, Vertex AI Batch Prediction Job and Two AI Agents built using Vertex AI Studio UI  | Provides a reproducible automation framework for model training, versioning, deployment, and daily batch prediction scoring, feeding classified results back to the DWH. |
| **Business Intelligence & Reporting** | Looker Studio Dashboard | To display pre-MLOps data visualizations such as trends and current status and post-MLOps data visualizations such as predictions, classifications and recommendations. |

---

 


 
