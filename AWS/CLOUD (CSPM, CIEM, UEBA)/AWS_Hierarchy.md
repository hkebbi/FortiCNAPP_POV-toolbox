
## 🏗️ AWS Organization Hierarchy: 

```mermaid
graph TD

A[🏠 Root Organization]:::root --> B1[📂 OU: Engineering]
A --> B2[📂 OU: Finance]
A --> B3[📂 OU: DevOps]

B1 --> C1[🧩 AWS Account: eng-prod-001]
B1 --> C2[🧩 AWS Account: eng-test-001]

B2 --> C3[🧩 AWS Account: fin-ops-001]
B2 --> C4[🧩 AWS Account: fin-dev-001]

B3 --> C5[🧩 AWS Account: devops-tools-001]

classDef root fill:#232f3e,stroke:#fff,stroke-width:2px,color:#fff;
classDef default fill:#1b4d89,stroke:#fff,stroke-width:1px,color:#fff;
classDef account fill:#3b82f6,stroke:#fff,stroke-width:1px,color:#fff;

class A root
class B1,B2,B3 default
class C1,C2,C3,C4,C5 account

```

-----
-----

## 🧩 AWS Identity & Access Concepts — and How They Relate in the Organization Hierarchy

| **Concept** | **What It Is** | **Where It Applies in the Hierarchy** | **Real-World Analogy** | **Relation to Others** |
|--------------|----------------|---------------------------------------|--------------------------|--------------------------|
| 🏢 **Service Control Policy (SCP)** | Organization-level *guardrails* that restrict what actions accounts can take — even if IAM allows them. | **Root** and **Account** levels in **AWS Organizations** (not OU yet in FortiCNAPP). | **Company-wide HR policy**: “No one can access payroll.” | SCPs are *outer boundaries* — they override IAM permissions. |
| 🗂️ **Organizational Unit (OU)** | A logical grouping of AWS accounts under a parent organization root. SCPs can be attached here too. | **Mid-level** between Root and Account. | **Department** (e.g., “Finance,” “Engineering”). | OUs inherit SCPs from their parent (Root). |
| 🧱 **Account** | An isolated AWS environment where users, roles, and resources live. | **Lowest level** in the Org hierarchy. | **Subsidiary or branch office.** | Each account enforces SCPs + IAM policies together. |
| 👥 **IAM (Identity and Access Management)** | The AWS service that manages *who can do what* inside a single account. | **Inside an Account** | **Company HR system** | IAM enforces identity-level permissions under the SCP limits. |
| 🧑‍💼 **IAM Role** | A temporary identity used by AWS services, users, or external systems to act in your account. | **Inside an Account** | **Job title** — e.g., “BackupManager” | Roles are governed by IAM policies and Trust policies. |
| 📜 **IAM Policy** | JSON-based permission document defining *what actions* are allowed or denied. | **Attached to IAM Roles, Users, or Groups** | **Job description** — defines allowed tasks. | Policies give specific permissions within the account. |
| 🔒 **Trust Policy (AssumeRole)** | Defines *who* can assume a role (e.g., from another account or service). | **Attached to IAM Roles** | **Badge access rule** — “Only people from Dept A can wear this badge.” | Controls **which principals** can use an IAM role. |
| 📦 **Resource Policy** | A policy *on a resource* (like an S3 bucket or KMS key) defining who can access it. | **At the resource level** inside an account. | **Guest list** on the resource itself. | Adds another layer of permissions *directly* on resources. |
