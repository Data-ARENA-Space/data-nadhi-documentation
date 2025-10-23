# Temporal in Data Nadhi

Data Nadhi uses **Temporal** to orchestrate and manage the execution of its data processing workflows.  
Temporal provides **reliability, scalability, and fault tolerance**, making it ideal for complex, long-running workflows.

---

## 🔄 Why Temporal?

Temporal is used in Data Nadhi because it solves key challenges in workflow orchestration:

- **Reliable execution:** Workflows are guaranteed to complete, even if a worker or service fails.  
- **Scalable:** Supports horizontal scaling by adding more workers to handle increased load.  
- **Fault tolerant:** Automatic retry and state recovery ensures no data loss.  
- **Code-first approach:** Workflows are written in standard programming languages, making them easy to maintain.  
- **State management:** Temporal automatically tracks workflow progress, so developers do not need to manage state manually.  

> 💡 *Temporal provides a durable, scalable, and easy-to-use workflow engine for orchestrating Data Nadhi pipelines.*

---

## 🏗️ Temporal Workflow Integration

### 1. **Data Nadhi Server**
- Receives incoming log data.  
- Validates the API key and identifies the organisation and project.  
- Pushes tasks into Temporal’s `task-q` queue for processing.  
- Refer [Server](/docs/architecture/server) for more info

### 2. **Data Nadhi Main Worker**
- Retrieves workflow configuration for each pipeline.  
- Prepares a traversable workflow structure and identifies the **start node**.  
- Pushes tasks into Temporal’s `task-q-transform` queue for transformation.  
- Refer [Main Worker](/docs/architecture/temporal/main-worker) for more info

### 3. **Data Nadhi Transformation Worker**
- Processes tasks from `task-q-transform`.  
- Applies **transformations**, **filters**, and **branching logic** based on the workflow.  
- Pushes transformed data into Temporal’s `task-q-destination` queue. 
- Refer [Transformation Worker](/docs/architecture/temporal/transformation-worker) for more info 

### 4. **Data Nadhi Destination Worker**
- Retrieves tasks from `task-q-destination`.  
- Fetches the appropriate **destination and connector configuration**.  
- Sends processed data to the configured target (DBs, APIs, etc.).
- Refer [Destination Worker](/docs/architecture/temporal/destination-worker) for more info   

---

## 🧱 Temporal Architecture Overview

- **Temporal Service:** Coordinates workflow execution and ensures durability.  
- **Workers:** Application-side processes that execute tasks defined in workflows.  
- **Workflows:** Code-defined sequences of tasks that Temporal manages and persists.  
- **Queues:** `task-q`, `task-q-transform`, and `task-q-destination` handle task distribution and sequencing.  

> 💡 *This architecture allows Data Nadhi to reliably orchestrate complex pipelines without manual state management.*

---

## ✅ Benefits of Using Temporal

- **Reliability:** Ensures workflows complete even in the presence of failures.  
- **Scalability:** Can handle high loads by adding more workers.  
- **Fault Tolerance:** Automatic retries and state recovery.  
- **Code-first Development:** Easy to write and maintain workflows in standard languages.  
- **Automatic State Management:** Removes the need to persist workflow state manually.
