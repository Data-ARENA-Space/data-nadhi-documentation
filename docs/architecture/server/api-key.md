# API Key Management

The **API Key system** in Data Nadhi ensures that only valid SDKs or clients can send logs to the Data Nadhi Server.  
It uses **three layers of AES-GCM encryption** to tightly bind each key to its **organisation** and **project**.

---

## 🧩 Overview

Each API Key is unique to a specific **organisation** and **project**.  
It is built using:
- **Project secret** (unique per project)  
- **Organisation secret** (unique per organisation)  
- **Global secret (SEC_GLOBAL)** (common to the system)  
- A fixed **default token string** used for validation  

> Both the *organisation secret* and *project secret* are stored in an **encrypted format** in MongoDB and only decrypted when needed.

---

## 🔐 Step-by-Step Key Creation

Below is the order of how an API Key is created:

1. **Start with a fixed internal token string** (used later for validation).  
```
token = DEFAULT_TOKEN_STRING
```
2. **First Encryption (enc1)**  
- Encrypt the token using the **Project Secret**.  
- This binds the key to a specific project.  
```
enc1 = AES-GCM(token, projectSecret)
```
3. **Second Encryption (enc2)**  
- Combine `enc1` and the `projectId`, then encrypt using the **Organisation Secret**.  
- This step connects the project to its organisation.  
```
enc2 = AES-GCM(enc1 | projectId, organisationSecret)
```
4. **Third Encryption (enc3)**  
- Combine `enc2` and the `orgId`, then encrypt using the **Global Secret (SEC_GLOBAL)**.  
- This final step ties the key to the overall system.  
```
enc3 = AES-GCM(enc2 | orgId, SEC_GLOBAL)
```
5. **Encode as Base64**  
- Convert the encrypted text to Base64 to form the final **API Key**.  
```
apiKey = base64(enc3)
```
---

## 🧾 Step-by-Step Key Validation

When the SDK sends a request to the server with the header `x-datanadhi-api-key`:

1. **Cache Lookup**  
- Redis is checked first.  
- If a valid or invalid result exists, it is immediately returned.

2. **Decryption (Reverse Order)**  
- Decrypt `enc3` with **Global Secret** → get `enc2` and `orgId`.  
- Decrypt `enc2` with **Organisation Secret** → get `enc1` and `projectId`.  
- Decrypt `enc1` with **Project Secret** → get the original **token**.

3. **Validate Token**  
- The decrypted token must match the fixed internal token.  
- If it doesn’t, the key is invalid and temporarily cached as a failed key.

4. **Cache the Result**  
- Valid results are cached with their `orgId` and `projectId` for faster lookups later.

---

## 🔐 About AES-GCM

Data Nadhi uses **AES-GCM (Advanced Encryption Standard – Galois/Counter Mode)** for all encryption and decryption.

📘 Learn more here: [AES-GCM Explained (Wikipedia)](https://en.wikipedia.org/wiki/Galois/Counter_Mode)

### 🧠 How It Works (In Simple Terms)
- Think of AES-GCM as a **safe box** with a **unique key** (the secret) and a **seal** (the GCM part).  
- When you encrypt something, AES locks it using the key and also stamps it with a verification tag.  
- When decrypting, it checks that stamp — if even a single character was changed, decryption fails.  
- This means AES-GCM not only hides your data but also ensures it hasn’t been tampered with.  
- Every secret (global, organisation, project) acts as a different key to a different safe.

---

## ⚙️ Error Handling

If the API Key:
- Is missing  
- Cannot be decrypted  
- Has a wrong token  
- Or points to a missing organisation/project  

…the server returns **401 Unauthorized** with a clear validation failure message.  
No sensitive information is exposed in any case.

---

## ⚡ Caching Behavior

- **Valid API Keys** are cached with their org/project IDs for quick access.  
- **Invalid keys** are cached briefly to prevent repeated validation attempts.  
- This heavy use of caching significantly reduces database reads and crypto operations.

---

## ✅ Summary

- Multi-layer AES-GCM encryption secures every API Key.  
- Organisation and project secrets are encrypted in storage.  
- The system is both **secure** (through encryption) and **fast** (through caching).  
- All invalid keys cleanly return 401s without breaking SDK flow.



