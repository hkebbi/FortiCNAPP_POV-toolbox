
🏢 Root Management Group
│
├── 📁 MG: Engineering
│   ├── 💠 Azure Subscription: eng-prod-001
│   └── 💠 Azure Subscription: eng-test-001
│
├── 📁 MG: Finance
│   ├── 💠 Azure Subscription: fin-ops-001
│   └── 💠 Azure Subscription: fin-dev-001
│
└── 📁 MG: DevOps
    └── 💠 Azure Subscription: dev-tools-001







| AWS Concept                         | Azure Equivalent                    | Description                                                                           |
| ----------------------------------- | ----------------------------------- | ------------------------------------------------------------------------------------- |
| **Root Organization**               | **Root Management Group**           | The top-level container in Azure. All resources and management groups exist under it. |
| **Organizational Unit (OU)**        | **Management Group (MG)**           | Logical containers that help organize subscriptions for policy, RBAC, and compliance. |
| **AWS Account**                     | **Azure Subscription**              | The billing and isolation boundary for Azure resources.                               |
| **Service Control Policies (SCPs)** | **Azure Policy / RBAC Assignments** | Used to enforce compliance and security standards.                                    |
