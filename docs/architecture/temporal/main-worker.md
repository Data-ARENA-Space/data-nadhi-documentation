# 🧠 Main Temporal Workflow

The **MainWorkflow** in Data Nadhi orchestrates the first phase of pipeline execution.  
It fetches pipeline and workflow configurations and then triggers the **TransformationWorkflow**.

---

## ⚙️ Overview

The workflow listens to Temporal’s **task queue** and starts whenever a new log or event arrives.  
It validates required metadata such as `organisationId`, `projectId`, and `pipelineId` before proceeding.

![Main Worker](/img/docs/main-worker.png)

---

## 🧩 Workflow Steps

### 1. Receive Input
Input payload contains:
- **Metadata**: organisation, project, pipeline, message IDs  
- **Log Data**: the event or log to process  

---

### 2. Fetch Pipeline Configuration
Activity: `fetch_pipeline_config`  

**Responsibilities:**
- Retrieve pipeline configuration from MongoDB  
- Validate pipeline existence and `startNodeId`  

If invalid, a failure is logged to **MinIO** and the workflow exits.

---

### 3. Fetch Workflow Configuration
Activity: `fetch_workflow_config`  

**Responsibilities:**
- Construct workflow from pipeline nodes  
- Validate workflow  
- Push workflow to **`task-q-transform`** queue  

If workflow creation fails, a failure is logged to **MinIO** and the workflow exits.

---

### 4. Trigger Transformation Workflow
- Starts **child workflow** `TransformationWorkflow`  
- Assigned to **`task-q-transform`** queue  
- Each run identified by appending `-transform` to parent workflow ID  

---

## 🚨 Failure Handling with MinIO

All workflow or activity failures are logged to **MinIO** for traceability.  
The failure logging includes:
- Exception type and message  
- Stack trace  
- Original and current input  
- Organisation, project, pipeline, and message IDs  

---

## ✅ Summary

The **MainWorkflow** in Data Nadhi orchestrates the first phase of log processing:  

- Validates incoming logs and required metadata (`organisationId`, `projectId`, `pipelineId`).  
- Fetches pipeline and workflow configurations from MongoDB.  
- Pushes workflows to the **task-q-transform** queue.  
- Triggers the **TransformationWorkflow** for node-level processing.  
- Logs failures to **MinIO** for traceability.  

This ensures **reliable orchestration, low-latency validation, and robust failure handling** before transformation begins.