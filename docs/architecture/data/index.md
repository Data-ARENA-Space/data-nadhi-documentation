# Data in Data Nadhi

This document provides an overview of the **data model and storage** used in Data Nadhi.  
Since we do not store raw log data permanently, our storage requirements are minimal compared to traditional logging systems.

---

## 🗄️ Data Stores Used

### 🍃 MongoDB
- Stores **transactional data** such as organisation information and pipeline workflow configurations.  
- Chosen for its **schemaless nature**, allowing flexibility as the schema is partially known.  
- Typical access pattern: mostly **single-row reads** using indexed fields.  
- Provides efficient retrieval for the core metadata of the system.
- For more details refer [Mongo DB for Data Nadhi](/docs/architecture/data/mongo)

### ⚡ Redis Cache
- Used to **cache data at each stage** of the pipeline for near real-time processing.  
- Proven reliable for fast, temporary storage.  
- Cache failures do **not affect the system**, though requests may incur cache misses. 
- For more details refer [Redis in Data Nadhi](/docs/architecture/data/redis) 

### 🐘 Postgres
- Stores data for **analytical purposes**.  
- Includes:
  - Organisation ID, Project ID, Pipeline ID  
  - Message ID, Node ID (workflow)  
  - Start and completion timestamps  
- Supports reporting and analytics on pipeline executions.

### ☁️ MinIO
- Stores **failed records** until user action is taken.  
- Chosen for its **compatibility with Amazon S3**, making future migration easier.  
- Stores:
  - Original log data  
  - Log data at the point of failure (after transformation)  
  - Organisation, project, pipeline, and message identifiers  
  - Timestamp and error information  
- For more details refer [MinIO in Data Nadhi](/docs/architecture/data/minio) 

---

## 🧩 Data Model

This section describes the **main entities** in Data Nadhi and their relationships:

- **Organisation** – Top-level entity representing a company or individual using the platform.  
- **Project** – A grouping of pipelines under an organisation.  
  - **API Key** is assigned at the project level.  
- **Pipeline** – Represents a single data pipeline.  
- **Pipeline Node** – Represents an individual node in a pipeline workflow.  
- **Integration Connector** – Represents an **instance of a destination**, e.g., a Slack bot or AWS account.  
- **Integration Target** – Represents the **specific destination** using the connector for credentials, e.g., a Slack message template or S3 bucket and prefix.

> 💡 *This structure allows each database and storage system to serve its optimal purpose while keeping the platform flexible and efficient.*