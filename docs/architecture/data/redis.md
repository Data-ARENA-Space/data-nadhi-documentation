# Redis Cache in Data Nadhi

Data Nadhi uses **Redis** as a caching layer to improve performance and provide near real-time access to frequently used data.  
This document explains **why Redis was chosen** and how the cache keys are structured.

---

## ⚡ Why Redis?

Redis is used because it fits the **high-performance, low-latency requirements** of Data Nadhi:

- **Fast in-memory storage:** Redis keeps data in memory, allowing **sub-millisecond reads and writes**, ideal for caching frequently accessed metadata.  
- **Simple key-value access:** Most of our cached data (organisations, projects, pipelines, API keys) is retrieved by unique keys, which maps perfectly to Redis.  
- **TTL support:** Redis allows setting **expiration times** for keys, helping keep cache fresh without manual cleanup.  
- **Non-critical cache:** Cache failures do **not break the system**, since the primary source of truth is still MongoDB. The system simply falls back to the database if a key is missing.  
- **Atomic operations:** Redis provides safe atomic operations, which is helpful for managing secrets and other metadata consistently.  

> 💡 *In short, Redis provides a lightweight, fast, and reliable caching layer that reduces database load and improves response times.*

---

## 🧩 Connection Management & Singleton Pattern

Both the **Python** and **Node.js** services in Data Nadhi use a **singleton-based Redis connection manager** to keep the cache layer lightweight and resilient.

### 🔁 Singleton Pattern
The Redis client follows a **singleton design**, meaning only **one connection instance** exists for the entire service process.  
When a component requests Redis access:
- If a client is already active, it is reused.  
- If not, a new client is created, connected, and stored as the single global instance.  

This avoids unnecessary reconnections, ensures predictable connection usage, and keeps Redis operations extremely fast.

### ⚙️ Connection Handling
The connection layer is designed for **stability** without letting cache failures affect the core workflow:
- **Non-blocking reconnection:** If Redis disconnects, the service attempts a single reconnection in the background without halting the main process.  
- **No hard retry loops:** Cache failures are logged, but the system continues operating normally using MongoDB as the fallback.  
- **Silent failure mode:** All cache operations (`safe_get`, `safe_set`, `del`) fail silently if Redis is unavailable — this guarantees that caching never breaks data flow.  
- **Connection monitoring:** The client logs key events like connection loss, recovery, and ping failures through the shared `Logger` utility.  

### ⚡ Optimized Cache Access
Both languages include *safe wrappers* (`safe_get` and `safe_set`) which automatically:
- Check connection health using a lightweight `PING`.  
- Attempt a single reconnect if needed.  
- Execute cache operations only when the connection is confirmed healthy.  

### 🧠 Result
This ensures Redis usage is:
- **Fast** — single shared client, minimal latency.  
- **Safe** — never blocks main operations due to cache failures.  
- **Self-healing** — auto-reconnects when possible and logs all failures cleanly.

> 💡 *In short, Data Nadhi treats Redis as a best-effort caching layer — optimized for speed, stable under failure, and always non-blocking.*

---

## 🔑 Cache Key Structure

Cache keys in Data Nadhi are **structured hierarchically** to reflect the relationships between organisations, projects, and pipelines:

- **API Keys:**  
  `datanadhiserver:apikey:<apiKey>` – Stores API key metadata for fast validation.  

- **Organisation data:**  
  `datanadhiserver:org:<orgId>` – Stores organisation metadata for quick retrieval.  

- **Project data:**  
  `datanadhiserver:org:<orgId>:prj:<projectId>` – Stores project-level information including API key and configuration.  

- **Pipeline data:**  
  - By code: `datanadhiserver:org:<orgId>:prj:<projectId>:plc:<pipelineCode>`  
  - By ID: `datanadhiserver:org:<orgId>:prj:<projectId>:pl:<pipelineId>`  

- **Workflow nodes:**  
  `datanadhiserver:org:<orgId>:prj:<projectId>:pl:<pipelineId>:workflow` – Stores the pipeline’s workflow nodes for fast access.  

- **Integration Connectors (decrypted credentials):**  
  `datanadhiserver:org:<orgId>:prj:<projectId>:ic:<connectorId>:decrypted` – Stores decrypted connector credentials for immediate use.  

- **Integration Targets:**  
  `datanadhiserver:org:<orgId>:prj:<projectId>:pl:<pipelineId>:it:<targetId>` – Stores the configuration of pipeline targets.  

- **Organisation & Project secrets:**  
  - Organisation secret: `datanadhiserver:org:<orgId>:secret`  
  - Project secret: `datanadhiserver:org:<orgId>:prj:<projectId>:secret`  

**Why this structure works:**  
- It mirrors the **hierarchical relationships** in the system (org → project → pipeline → workflow → connector/target).  
- Enables **fast lookups** using a single key without complex queries.  
- Makes it easy to **invalidate or update** keys at any level (organisation, project, pipeline, workflow, or integration).  

> 💡 *This design keeps the cache simple, fast, and fully aligned with the main data model in MongoDB.*
