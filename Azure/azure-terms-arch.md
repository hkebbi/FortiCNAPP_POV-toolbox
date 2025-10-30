## 🏗️ Azure Management Hierarchy:

---
```mermaid
graph LR
  root(["Root Management Group"])
  root --> eng["MG: Engineering"]
  root --> fin["MG: Finance"]
  root --> dev["MG: DevOps"]

  eng --> eng_prod["Subscription: eng-prod-001"]
  eng --> eng_test["Subscription: eng-test-001"]
  fin --> fin_ops["Subscription: fin-ops-001"]
  fin --> fin_dev["Subscription: fin-dev-001"]
  dev --> dev_tools["Subscription: dev-tools-001"]

  classDef mg fill:#1f4ed8,stroke:#0ea5e9,color:#fff,rx:6,ry:6;
  classDef sub fill:#3b82f6,stroke:#1e40af,color:#fff,rx:6,ry:6;

  class root,eng,fin,dev mg;
  class eng_prod,eng_test,fin_ops,fin_dev,dev_tools sub;

```
---
---

| AWS Concept                         | Azure Equivalent                    | Description                                                                           |
| ----------------------------------- | ----------------------------------- | ------------------------------------------------------------------------------------- |
| **Root Organization**               | **Root Management Group**           | The top-level container in Azure. All resources and management groups exist under it. |
| **Organizational Unit (OU)**        | **Management Group (MG)**           | Logical containers that help organize subscriptions for policy, RBAC, and compliance. |
| **AWS Account**                     | **Azure Subscription**              | The billing and isolation boundary for Azure resources.                               |
| **Service Control Policies (SCPs)** | **Azure Policy / RBAC Assignments** | Used to enforce compliance and security standards.                                    |

---
---
### 🧩 Azure Identity & Access Concepts — and How They Relate in the Management Hierarchy

| **Concept**                              | **What It Is**                                                                                               | **Where It Applies in the Hierarchy**                     | **Real-World Analogy**                        | **Relation to Others**                                                              |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------- | --------------------------------------------- | ----------------------------------------------------------------------------------- |
| 🏢 **Management Group**                  | Logical container that organizes Azure subscriptions under the root for unified policy and RBAC.             | **Above Subscriptions**, under **Root Management Group**. | **Department or Division**                    | Policies and RBAC inherit downward.                                                 |
| 💠 **Subscription**                      | Boundary for billing, quotas, and access. Holds Resource Groups and resources.                               | **Below Management Groups**                               | **Business Unit or Project**                  | Receives inherited governance from MG.                                              |
| 🤝 **Managed Identity**                  | Identity for a resource to authenticate to Azure services securely.                                          | **At Resource Level**                                     | **Employee Badge**                            | Removes the need for stored credentials.                                            |
| 🧩 **Entra ID (Azure Active Directory)** | Identity provider for all Azure identities and authentication.                                               | **Tenant-wide, outside MG hierarchy.**                    | **Corporate Directory / HR System.**          | Central to RBAC, policies, and authentication.                                      |
| 🧭 **Microsoft Graph API**               | Unified API endpoint (`graph.microsoft.com`) to query, manage, and automate Microsoft 365 and Entra ID data. | **Tenant-wide**                                           | **Company Directory Database API.**           | Replaces Azure AD Graph API; used for managing users, groups, roles, policies, etc. |
| ⚙️ **Azure Resource Graph**              | Query engine for exploring and analyzing resources across subscriptions.                                     | **Across MGs and Subscriptions**                          | **Company Asset Tracker / Inventory Search.** | Provides resource-level visibility via `az graph query` or ARM REST API.            |
| 🗂️ **Resource Group**                   | Logical container for related Azure resources.                                                               | **Inside a Subscription**                                 | **Project Folder**                            | Groups resources for lifecycle and access management.                               |
| 🧱 **Resource**                          | Any deployable Azure entity (VM, Storage, App Service, etc.).                                                | **Inside a Resource Group**                               | **Individual Asset**                          | Enforces inherited policies and RBAC.                                               |
| 🔐 **Azure Policy**                      | Rules for compliance and governance (e.g., enforce allowed regions or VM sizes).                             | **At any level (MG, Subscription, RG)**                   | **Compliance Manual**                         | Works with RBAC — defines *what can exist*.                                         |
| 👥 **RBAC (Role-Based Access Control)**  | Authorization system defining who can do what to which resources.                                            | **At any level (MG → Resource)**                          | **HR Permission Matrix**                      | Uses roles and assignments tied to Entra ID identities.                             |
| 🧑‍💼 **Role Definition**                | Set of actions a role allows (e.g., Reader, Contributor).                                                    | **Part of RBAC**                                          | **Job Title**                                 | Defines allowed actions.                                                            |
| 🪪 **Role Assignment**                   | Binds a Role to a User/Group/Service Principal at a scope.                                                   | **Any level**                                             | **HR Assignment (“Alice is Manager”)**        | Enforces access rights.                                                             |
| 📜 **Blueprint / Policy Initiative**     | Bundle of policies, roles, and ARM templates for standard setups.                                            | **At MG or Subscription**                                 | **Company Playbook / Compliance Kit**         | Ensures consistent deployment.                                                      |


---
---

```mermaid
graph TD

  %% Identity & Directory
  A["🌐 Entra ID (Azure Active Directory)"]
  B["🧭 Microsoft Graph API"]
  C["🤝 Managed Identities"]
  D["⚙️ Azure Resource Graph"]

  A --> B
  A --> C
  B --> A

  %% Management Hierarchy
  Z["🏢 Root Management Group"]
  Y["📁 MG: Engineering"]
  X["📁 MG: Finance"]
  W["📁 MG: DevOps"]

  Z --> Y
  Z --> X
  Z --> W

  %% Subscriptions & Resources
  Y --> F["💠 Subscriptions"]
  X --> F
  W --> F
  F --> G["🗂️ Resource Groups"]
  G --> H["🧱 Resources"]

  %% Governance & Control
  I["🔐 Azure Policy"]
  J["👥 RBAC"]
  K["🧑‍💼 Role Definitions"]
  L["🪪 Role Assignments"]
  M["📜 Blueprints / Initiatives"]

  I --> Z
  I --> F
  J --> F
  J --> G
  J --> H
  K --> J
  L --> J
  M --> I
graph TD

  %% Identity & Directory
  A["🌐 Entra ID (Azure Active Directory)"]
  B["🧭 Microsoft Graph API"]
  C["🤝 Managed Identities"]
  D["⚙️ Azure Resource Graph"]

  A --> B
  A --> C
  B --> A

  %% Management Hierarchy
  Z["🏢 Root Management Group"]
  Y["📁 MG: Engineering"]
  X["📁 MG: Finance"]
  W["📁 MG: DevOps"]

  Z --> Y
  Z --> X
  Z --> W

  %% Subscriptions & Resources
  Y --> F["💠 Subscriptions"]
  X --> F
  W --> F
  F --> G["🗂️ Resource Groups"]
  G --> H["🧱 Resources"]

  %% Governance & Control
  I["🔐 Azure Policy"]
  J["👥 RBAC"]
  K["🧑‍💼 Role Definitions"]
  L["🪪 Role Assignments"]
  M["📜 Blueprints / Initiatives"]

  I --> Z
  I --> F
  J --> F
  J --> G
  J --> H
  K --> J
  L --> J
  M --> I

```
