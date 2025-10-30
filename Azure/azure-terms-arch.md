## 🏗️ Azure Management Hierarchy:

---

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
