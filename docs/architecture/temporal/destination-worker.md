# 📦 Destination Workflow

The **DestinationWorkflow** in Data Nadhi handles the final step of the pipeline: delivering processed logs to their intended destinations.  
It receives data from the **TransformationWorkflow** and ensures proper integration with external systems.

---

## ⚙️ Overview

This workflow is triggered as a **child workflow** from the **TransformationWorkflow**.  
It fetches required **integration targets and connector configurations**, and sends the data to the destination.

![Destination Worker](/img/docs/destination-worker.png)

---

## 🧩 Workflow Steps

### 1. Fetch Integration Target
Activity: `fetch_integration_target`  

**Responsibilities:**
- Retrieve the target configuration using `target_id`  
- If target not found, the workflow throws an error  
- Provides target details to the next step

---

### 2. Fetch Integration Connector
Activity: `fetch_integration_connector`  

**Responsibilities:**
- Retrieve the connector configuration using the connector ID from the target  
- Decrypt sensitive credentials securely  
- If connector or credentials not found, throw an error  
- Provides connector details to the destination activity

---

### 3. Send to Destination
Activity: `send_to_destination`  

**Responsibilities:**
- Uses target and decrypted connector data to send logs/events  
- Handles failures at this stage and retries according to default retry policy  

---

### 4. Exit
- Workflow completes successfully once data is sent to the destination  
- Failure at any step logs detailed information to **MinIO** for traceability

---

## 🚨 Failure Handling

All activity failures are logged to **MinIO**. Each log includes:
- Exception type and message  
- Stack trace  
- Input data that caused the failure  
- Target and connector IDs  

This ensures transparency and facilitates troubleshooting in case of delivery issues.

---

## ✅ Summary

The **DestinationWorkflow** guarantees reliable delivery of processed logs to external systems by:
- Validating targets and connectors before sending  
- Securely handling credentials within the connector fetch activity  
- Handling errors gracefully and logging all failures to MinIO  

This ensures that every log reaches its intended destination securely and reliably.
