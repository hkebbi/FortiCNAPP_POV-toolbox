## 🏗️ Azure Management Hierarchy:

---
graph TD
    A[🏢 Root Management Group] --> B[📁 MG: Engineering]
    A --> C[📁 MG: Finance]
    A --> D[📁 MG: DevOps]

    B --> E[💠 Azure Subscription: eng-prod-001]
    B --> F[💠 Azure Subscription: eng-test-001]

    C --> G[💠 Azure Subscription: fin-ops-001]
    C --> H[💠 Azure Subscription: fin-dev-001]

    D --> I[💠 Azure Subscription: dev-tools-001]


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
