# Architecture Overview

Data Nadhi is a **distributed, microservices-based data pipeline platform** designed for flexibility, scalability, and simplicity.  
This document provides a high-level view of the system and links to detailed architecture pages for each component.

---

## 🏗️ High-Level Architecture

Data Nadhi is composed of multiple independently deployable services that work together to ingest, process, and deliver data across systems.

![Data Nadhi Architecture](/img/docs/core-architecture.png)

---

## 📦 Core Components

### 🧩 Data Nadhi SDK
- Implemented as **language-specific SDKs**, one per repository.
- Responsible for capturing logs and sending them securely to the Data Nadhi Server.
- Requires:
  - **API Key** for authentication.
  - **Log Config** to define which logs go to stdout and which flow to the server.
- On failure, the SDK logs the error (if enabled) after receiving a failure response from the server.
- **Available SDKs:**
  - 🐍 [Python SDK](https://github.com/Data-ARENA-Space/data-nadhi-sdk/)
- 📘 [Detailed SDK Architecture →](/docs/architecture/sdk/index)

---

### ⚙️ Data Nadhi Server
- A **Node.js-based API server** designed for horizontal scalability.
- Core responsibilities:
  - Validate API Key and extract org/project information.
  - Verify if the requested pipeline is active.
  - Push log events to Temporal’s `task-q` queue.
- 🗃️ [Repository](https://github.com/Data-ARENA-Space/data-nadhi-server/)  
- 📘 [Detailed Server Architecture →](/docs/architecture/server/index)

---

### 🔄 Data Nadhi Main Worker
- Retrieves workflow configuration for each pipeline and prepares a traversable structure.
- Determines the **start node** and pushes tasks to Temporal’s `task-q-transform`.
- 🗃️ [Repository](https://github.com/Data-ARENA-Space/data-nadhi-temporal-worker/)  
- 📘 [Detailed Overview →](/docs/architecture/temporal/main-worker)

---

### 🧠 Data Nadhi Transformation Worker
- Traverses workflow configuration and applies transformations.
- Handles:
  - `transform` nodes  
  - `filter`, `branch`, and `conditional-branch` nodes
- Pushes transformed data to Temporal’s `task-q-destination`.
- 🗃️ [Repository](https://github.com/Data-ARENA-Space/data-nadhi-temporal-worker/)  
- 📘 [Detailed Overview →](/docs/architecture/temporal/transformation-worker)

---

### 🚚 Data Nadhi Destination Worker
- Fetches destination and connector configurations.
- Chooses the correct **destination sender engine**.
- Sends the processed data to the configured target (DBs, APIs, etc.).
- 🗃️ [Repository](https://github.com/Data-ARENA-Space/data-nadhi-temporal-worker/)  
- 📘 [Detailed Overview →](/docs/architecture/temporal/destination-worker)

---

## 🧰 Utility Components

### 🧱 Data Nadhi Dev
- Contains the **development environment** setup using Dev Containers.
- Includes:
  - `docker-compose` with shared storage and infrastructure.
  - Migration scripts for **MongoDB** and **Postgres**.
- 🗃️ [Repository](https://github.com/Data-ARENA-Space/data-nadhi-dev/)

---

### 🧩 Data Nadhi Internal Server
- A lightweight server used for **intermediate operations** (typically handled by the UI).
- Reduces manual effort during testing when the frontend is unavailable.
- 🗃️ [Repository](https://github.com/Data-ARENA-Space/data-nadhi-internal-server/)

---

> 💡 *All components are modular and containerized, making the entire system portable, scalable, and ready for production.*

