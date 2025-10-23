# Server Architecture

The **Data Nadhi Server** is the central entry point for all logs and events sent from SDKs.  
It validates, enriches, and routes log data into the configured pipelines using **Temporal** for asynchronous processing.  
The system is built to be **stateless**, **heavily cached**, and **fault-tolerant**.

---

## 🧩 Overview

![Data Nadhi Server](/img/docs/server.png)

---

## ⚙️ Core Responsibilities

- Authenticate SDK requests using organisation-level secrets.  
- Fetch and validate project and pipeline configurations.  
- Use **extensive caching** to minimize MongoDB queries.  
- Enqueue data into **Temporal workflows** for async processing.  
- Provide structured responses for synchronous validation.  
- Push async workflow failures into **MinIO** for post-processing and debugging.

---

## ⚡ Extensive Use of Caching

Caching is a critical part of the server architecture.  
Almost every data access path — such as fetching organisation secrets, project configurations, or pipeline definitions — is served from the cache first.

When a cache miss occurs:
1. The data is fetched from **MongoDB**.  
2. Decrypted or transformed if needed.  
3. **Written back to the cache** with a defined TTL.  

This read-through and write-back pattern ensures that:
- Database calls are minimal.  
- Secrets and configs are instantly accessible.  
- Server latency remains consistently low, even under high load.

🧠 **In short:**  
Every piece of reusable data flows through the cache before hitting the database.

---

## 🔁 Request Processing Flow

The processing is divided into two main parts — **Sync Flow** (validation and acknowledgment) and **Async Flow** (background processing via Temporal).

### **Sync Flow**
1. **Validate API Key** — Extract and verify organisation secret. Refer [API Key Management](/docs/architecture/server/api-key) for more info
2. **Attach Identifiers** — Add `org_id` and `project_id` to the request.  
3. **Fetch Pipeline Config** — Resolve using `pipelineCode`, `org_id`, and `project_id` from cache or Mongo.  
4. **Validate Pipeline**
   - Return **401** if API key is invalid.  
   - Return **400** if payload or pipelineCode is missing.  
   - Return **404** if pipeline not found.  
   - Return **400** if pipeline is inactive.  
   - Return **500** if any unexpected internal error occurs.  
5. **Generate Message ID** — On success, acknowledge with `200 OK`.

---

### **Async Flow**
1. **Generate Metadata** — Create structured log metadata for processing.  
2. **Generate Workflow ID** — Create a unique ID for each pipeline execution.  
3. **Resolve Processor ID** — Select the Temporal task queue ID based on priority:  
```
pipeline-level > project-level > org-level > default
```
4. **Push to Temporal Queue** — Enqueue workflow execution into Temporal for downstream processing.

---

## 🚨 Exception Handling

The server follows a two-tiered error handling approach:

### **Synchronous Errors**
- These occur during validation or metadata generation (before Temporal handoff).
- The SDK immediately receives an error response (e.g., `401`, `400`, `404`, `500`).
- No retry is performed at this stage.

### **Asynchronous Errors**
- Once the workflow is handed off to Temporal, the SDK is no longer involved.
- Any failures during async processing (e.g., destination connection errors, transformation issues) are captured and logged to **MinIO**.
- These logs are later used for debugging and analysis.

---

## 🧱 Data Access and Structure

Data is accessed through well-defined keys that uniquely represent each resource:

| Purpose | Key Format |
|----------|-------------|
| Organisation Secret | `datanadhiserver:org:<orgId>:secret` |
| Pipeline By `pipelineCode` | `datanadhiserver:org:<orgId>:prj:<projectId>:plc:<pipelineCode>` |
| API Key validation metadata | `datanadhiserver:apikey:<apiKey>` |

---

## ✅ Summary

The **Data Nadhi Server** acts as a secure, high-performance orchestrator between SDKs and the Temporal processing engine.  
It achieves **speed and reliability** by:
- Performing **all reads/writes through cache**.  
- Validating everything synchronously before workflow execution.  
- Offloading heavy processing to Temporal.  
- Safely handling async failures via **MinIO**.

This design ensures consistent behavior, low latency, and robust failure isolation across all log ingestion workflows.
