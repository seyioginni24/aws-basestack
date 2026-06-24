# 🏢 Lagos Law Firm - AWS S3 Document Management System (DMS)

## 📌 Architecture Overview
This project simulates a secure, cloud-native Document Management System (DMS) built on Amazon S3 for a law firm based in Lagos[cite: 9]. The architecture prioritises data durability, regulatory compliance, and cost-optimized storage tiering.

[cite_start]Documents are logically separated using S3 prefixes to simulate a traditional folder structure[cite: 21]:
* [cite_start]`/contracts/` (e.g., client agreements) [cite: 22]
* [cite_start]`/correspondence/` (e.g., client communications) [cite: 23]
* [cite_start]`/filings/` (e.g., court documents) [cite: 24]

**Hybrid Cloud Integration (Bonus Architecture)**
[cite_start]To bridge the gap between legacy on-premises infrastructure and the cloud[cite: 68]:
* [cite_start]**AWS DataSync** is utilised to execute the initial bulk migration of historical case files from the firm's existing on-premises file server to Amazon S3[cite: 68].
* [cite_start]**AWS Storage Gateway (File Gateway)** is deployed locally to provide an NFS mount point[cite: 68]. [cite_start]This allows the legal team to interact with S3-backed files using their standard desktop file explorer, requiring zero retraining while leveraging limitless cloud backend storage[cite: 68].

## 🔒 Security Controls
Strict access management and data protection are enforced at the bucket and object levels:
* [cite_start]**Block All Public Access:** Enabled globally on the bucket (`lagos-lawfirm-docs-*`) to prevent accidental data exposure[cite: 17, 18].
* [cite_start]**Encryption at Rest:** All objects are encrypted by default using Server-Side Encryption with Amazon S3 managed keys (SSE-S3)[cite: 18].
* [cite_start]**Data Recovery & Integrity:** S3 Versioning is enabled to protect against accidental overwrites or malicious deletions, ensuring historical copies of all legal drafts are retained[cite: 9, 18].
* [cite_start]**Resource Tagging:** Implemented tags (`Project=LawFirmDMS`, `Environment=Production`, `Owner=Admin`) for billing visibility and IAM policy conditions[cite: 19].

## ⏳ Data Lifecycle Policy (`LawFirmArchive`)
Legal documents are subject to strict regulatory retention laws. [cite_start]An automated S3 Lifecycle Rule is configured for all objects in the bucket to optimize costs over a 7-year timeline[cite: 27, 32]:

| Object Age | Storage Tier | Rationale |
| :--- | :--- | :--- |
| **0 - 90 Days** | S3 Standard | Frequent access during active casework. |
| **90 Days** | Glacier Instant Retrieval | [cite_start]Case closed; files rarely accessed but require immediate retrieval if an appeal occurs[cite: 28]. |
| **365 Days** | Glacier Flexible Retrieval | [cite_start]Deep archive for long-term storage, yielding significant cost savings[cite: 31]. |
| **2555 Days (7 Years)**| Permanent Deletion (Expiration) | [cite_start]Automated purging to comply with data privacy regulations once the legal retention period ends[cite: 32]. |


## 🔗 Pre-Signed URL Workflow (Time-Limited Access)
When the legal team needs to share specific case files with external clients or opposing counsel, they do not create permanent IAM users. [cite_start]Instead, we utilise **S3 Pre-Signed URLs**[cite: 9, 61].

[cite_start]Using the AWS CLI, temporary, authenticated links are generated granting read-only access to a specific document[cite: 33, 37]. 

**Workflow:**
1. [cite_start]Administrator requests a temporary link via CLI: `aws s3 presign s3://lagos-lawfirm-docs-.../contracts/sample-contract.pdf --expires-in 3600` [cite: 37]
2. [cite_start]The URL is shared with the client, who can download the file directly from their browser without needing AWS credentials[cite: 39].
3. [cite_start]Exactly 1 hour (3600 seconds) later, the cryptographic signature expires[cite: 37, 40]. [cite_start]Any subsequent access attempts yield an `AccessDenied` error[cite: 40, 57].


## 💰 Cost Estimate & Business Value
* **Storage Optimization:** By aggressively transitioning data from standard S3 ($0.023/GB) to Glacier Flexible Retrieval ($0.0036/GB) after one year, the firm reduces long-term storage costs by over 80%.
* **Zero Server Maintenance:** As a serverless S3 architecture, the firm pays zero compute costs and eliminates the need for patching, power, or cooling associated with their old on-premises SAN arrays.
* **Disaster Recovery:** Built-in multi-AZ redundancy provides 11 9s of durability, ensuring critical legal records survive localized natural disasters or hardware failures.

