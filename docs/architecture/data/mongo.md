# MongoDB for Data Nadhi

Data Nadhi uses **MongoDB** for transactional and metadata storage.  
This document explains **why MongoDB was chosen** and provides an overview of the main collections.

---

## 🍃 Why MongoDB?

MongoDB is used because it fits the **flexible and dynamic nature** of Data Nadhi’s metadata and workflow configuration:

- **Schemaless flexibility:** Many entities like pipelines, nodes, and connectors have configurations that can vary. MongoDB allows storing these as documents without a rigid schema.  
- **Hierarchical data support:** Entities like pipelines, nodes, and integration targets have nested relationships that map naturally to document structure.  
- **Frequent single-row access:** Most queries fetch **one organisation, project, pipeline, or node** at a time, which MongoDB handles efficiently with indexed fields.  
- **Ease of updates and changes:** Node configurations and connector credentials can change over time. MongoDB allows modifying documents without affecting unrelated data.  
- **Rapid development:** New fields or changes in workflow structure can be accommodated without database migrations.  

> 💡 *In short, MongoDB provides the right mix of flexibility, simplicity, and efficiency for storing Data Nadhi’s transactional metadata.*

---

## ⚙️ Connection Management & Singleton Pattern

Both the **Python** and **Node.js** services in Data Nadhi use a **singleton-based MongoDB connection manager**.

### 🧩 Singleton Pattern
The Mongo connection manager follows a **singleton design**, meaning only **one instance** of the Mongo client exists throughout the lifecycle of the service.  
This avoids multiple redundant connections and ensures all database operations share a single, pooled client.

When a service first requests a database connection:
- If a client instance already exists, it is reused.
- If not, a new client is created, connected, and cached as the singleton instance.

This approach ensures:
- Consistent access to MongoDB across all modules.
- Controlled connection pooling and minimal resource overhead.
- Simplified health checking and reconnection logic.

### 🌐 Connection Handling
The connection layer performs several built-in checks and protections:
- **Health verification:** Each access triggers a lightweight `ping` to ensure the connection is alive.  
- **Automatic reconnection:** If the `ping` fails, the service attempts a single reconnect before raising an error.  
- **Connection pooling:** Clients are configured with bounded pool sizes (min/max), socket timeouts, and retry behavior for reads/writes.  
- **Centralized logging:** Any failure to connect, ping, or reconnect is logged through the standard `Logger` utility.  

### 🧠 Result
This ensures MongoDB access is:
- **Reliable** — automatically recovers from transient disconnections.  
- **Efficient** — reuses connections instead of repeatedly opening and closing them.  
- **Consistent** — one Mongo client instance is used throughout the entire process lifecycle.

> 💡 *In short, Data Nadhi keeps Mongo connections lightweight, monitored, and resilient using a singleton pattern with built-in reconnection and health-check logic.*

---

## 🗂️ Collections Schema Overview

### Organisations
Represents an organisation or individual using the platform:

- `organisationName` – Name of the organisation  
- `organisationId` – Auto-generated UUID  
- `organisationSecretEncrypted` – Organisation-level secret for API key generation  
- `processorId` – Optional; indicates a dedicated queue at organisation level  

### Projects
Grouping of pipelines under an organisation. API Keys are generated at this level:

- `organisationId`  
- `projectName`  
- `projectId` – Auto-generated UUID  
- `projectSecretEncrypted` – Project-level secret for API key  
- `processorId` – Optional; dedicated queue at project level  

### Pipelines
Represents an individual pipeline in a project:

- `organisationId`, `projectId`  
- `pipelineName`, `pipelineId` – Auto-generated UUID  
- `active` – Indicates if the pipeline is active  
- `pipelineCode` – Code users include in their log config  
- `processorId` – Optional; dedicated queue at pipeline level  
- `startNodeId` – Node where workflow execution begins  

### PipelineNodes
Stores workflow nodes for each pipeline:

- `organisationId`, `projectId`, `pipelineId`  
- `nodeId` – Auto-generated UUID  
- `nodeConfig` – Configuration for the node, including:
  - `name` – Node name  
  - `type` – `condition-branching`, `transformation`, or `end`  
  - `filters` – Config for `condition-branching` nodes  
  - `transformation_fn` – Function name for `transformation` nodes  
  - `transformation_params` – Parameters for the transformation function  
  - `target_id` – ID of the target for `end` nodes  
  - `input_type` – Optional, intended for validation  
  - `output_type` – Data type after transformation  

### IntegrationConnectors
Top-level destination credentials at the project level:

- `organisationId`, `projectId`  
- `connectorName`, `connectorId` – Auto-generated UUID  
- `integrationType` – e.g., Slack bot, AWS IAM  
- `encryptedCredentials` – Credentials stored in encrypted JSON  

### IntegrationTargets
Detailed destination configuration at the pipeline level:

- `organisationId`, `projectId`, `pipelineId`  
- `connectorId`  
- `destinationName`, `targetId` – Auto-generated UUID  
- `destinationParams` – JSON with destination-specific details, e.g., S3 prefix or Slack channel  

> 💡 *MongoDB’s document model aligns naturally with this nested, flexible structure, making it easy to store and query pipeline metadata efficiently.*
