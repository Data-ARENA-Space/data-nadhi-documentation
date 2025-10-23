# MinIO in Data Nadhi

Data Nadhi uses **MinIO** to store **failed records** and related metadata.  
MinIO is chosen because it is **S3-compatible**, lightweight, and allows easy migration to AWS S3 in the future.

---

## ☁️ Why MinIO?

- **Failure handling:** Stores logs that fail during processing, ensuring no data is lost.  
- **S3-compatible:** Makes it easy to switch to AWS S3 or other compatible storage if needed.  
- **Original and transformed data:** Keeps both the original log and the current input at the point of failure.  
- **Durable storage:** Allows later inspection or reprocessing of failed records.  

> 💡 *MinIO acts as a “safety net” for the platform, ensuring failed data is stored reliably for investigation or retries.*

---

## 🗂️ Failure Log Schema

Each failure log is stored as a **JSON object** in MinIO, organized using a folder structure based on organisation, project, pipeline, and message IDs.  

### Fields:

- `timestamp` – ISO timestamp of the failure.  
- `organisationId` – ID of the organisation that owns the pipeline.  
- `projectId` – ID of the project where the pipeline belongs.  
- `pipelineId` – ID of the pipeline where the failure occurred.  
- `messageId` – Unique ID of the log message.  
- `originalInput` – Original log data received by the system.  
- `currentInput` – Input at the point of failure (after partial transformations).  
- `error` – Object containing:
  - `message` – Error message.  
  - `type` – Exception type.  
  - `stack` – Stack trace.  
  - `description` – Human-readable description of the error.  
- `context` *(optional)* – Extra context added during failure handling.  

---

## 🏗️ Storage Structure

- **Folder hierarchy:** `<organisationId>/<projectId>/<pipelineId>/<messageId>/`  
- **Filename:** `<activity_name>-<timestamp>.json`  
- **Example:**  
```
11111111-1111-1111-1111-111111111111/
22222222-2222-2222-2222-222222222222/
33333333-3333-3333-3333-333333333333/
TransformationWorkflow-transform-20251023120000123456.json
```

**Why this structure works:**  
- Organizes failed logs **by hierarchy**, making it easy to find records per organisation, project, and pipeline.  
- Keeps **timestamps and activity names** in filenames for versioning and quick inspection.  
- Stores both **original and current input**, ensuring full traceability of failures.  

> 💡 *This setup ensures that all failures are durable, easily accessible, and ready for reprocessing or debugging.*
