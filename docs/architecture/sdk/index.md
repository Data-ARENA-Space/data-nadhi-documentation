# SDK Architecture

The Data Nadhi SDK provides client libraries that capture logs from user applications and send them to the Data Nadhi Server when required.  
It is designed to work seamlessly with the existing logging systems of each supported language while enabling advanced pipeline-based log routing.

## Overview

![Data Nadhi SDK](/img/docs/sdk.png)

---

## 🧩 Core Components

### Logger Utility
- Uses the **default logging module** in each language (no custom framework).
- Can be used independently — even without pipeline triggering.
- When pipelines are not active, logs are written to **stdout** like normal logs.
- Ensures that integrating Data Nadhi doesn’t change existing logging behavior.

### Log Config
- The log configuration supports **conditions** on nested JSON fields.
- Allows **rule-based control** on whether a log should be written to stdout or sent to pipelines.
- Supports **multiple pipelines per rule**.
- Includes logical **AND / OR** operators for flexible filtering.
- For detailed schema and examples, read [here](/docs/architecture/sdk/log-config).

### API Key
- Required for sending logs to Data Nadhi pipelines.
- Can be set as an environment variable:  
  ```bash
  export DATA_NADHI_API_KEY=<your_api_key>
  ```
- Can also be passed during Logger initialization (not recommended except for testing).

### Formatter
- The default formatter is JSON.
- You can define custom formatters — since it builds on top of native logging, all usual formatting options work.
- Fallback is always the default formatter to avoid crashes during logging.

### Log Builder
- Responsible for creating a standardized structured payload before sending logs to the server.
- Captures consistent metadata for each log, including `timestamp`, `file info`, `function name`, and `trace ID`.
- This payload is internal to Data Nadhi and may differ from the formatter output shown in stdout.
- See detailed field-level explanation [here](/docs/architecture/sdk/log-structure)

---

## ⚙️ Architecture Flow
The SDK works in three main stages:
1. Initialization (on application load)
    - Initializes the logger.
    - Loads the log config from YAML.
    - Converts it into a traversable structure.
    - Fetches the API key from the environment (or takes the one passed).
    - Sets the formatter (custom or default).
2. Logging (when logs are emitted)
    - Generates the log data.
    - Checks whether the log passes the defined rules.
    - If stdout is enabled, prints the log locally.
    - If pipelines are configured, sends the log asynchronously to the Data Nadhi Server using the API key.
3. Response Handling
    - On response, the SDK checks for any log_datanadhi_errors.
    - If errors are found, they are logged to stdout using the configured formatter.
    - All server interaction happens asynchronously, ensuring zero blocking on the main application thread.

---

## 🚨 Error Handling
- Failed requests or timeouts are logged to stdout (if enabled).
- Invalid API keys or malformed log configs raise descriptive errors during initialization.
- Network issues do not interrupt the main application — the SDK safely falls back to local logging.

---

## 🌍 Supported Languages
### Python SDK
- **Repository:** [data-nadhi-sdk](https://github.com/Data-ARENA-Space/data-nadhi-sdk)
- Uses threading for asynchronous log sending to the Data Nadhi Server.

---

## 🧱 Extending the SDK
To implement the SDK in other languages:
- Follow the same **initialization → log traversal → async send pattern**.
- Use the same **log config schema** and **API key validation** logic.
- Maintain **stdout fallback** as the default mode.
- Ensure async or non-blocking behavior for sending logs.
Refer to the [Python SDK](https://github.com/Data-ARENA-Space/data-nadhi-sdk) as a base implementation.

---

**In short:**
The Data Nadhi SDK acts as a lightweight, non-intrusive logging layer that integrates easily, works reliably offline or online, and ensures your data always flows through the right pipelines — without affecting your app performance.