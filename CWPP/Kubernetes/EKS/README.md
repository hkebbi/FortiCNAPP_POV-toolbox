## 🛡️ EKS agent Deployment

### ☸️ Lacework FortiCNAPP Kubernetes Agent Deployment Methods

| **Deployment Method** | **Description** | **Best For / When to Use** |
|------------------------|-----------------|-----------------------------|
| 🧩 **Install with a Helm Chart** | **Helm** is a package manager for Kubernetes that bundles resources into versioned “charts.” You can download the Lacework FortiCNAPP Helm chart and install it easily across clusters. | ✅ Best for **simplified installation and upgrades**.<br>Ideal if you use **Helm** for app deployment and prefer declarative configuration management. |
| 🔁 **Deploy with a DaemonSet** | A **DaemonSet** ensures that a copy of the agent runs on **every node** in your Kubernetes cluster. Useful for continuous monitoring across all nodes (e.g., AKS, EKS, GKE). | ✅ Best for **broad node-level coverage**.<br>Ideal for **manual or scripted installs** in environments without Helm or Terraform. |
| ⚙️ **Install with Terraform** | Uses the **Terraform Kubernetes Agent Module** to automatically create secrets and DaemonSets required for deployment. This method integrates Lacework FortiCNAPP into infrastructure-as-code workflows. | ✅ Best for **infrastructure automation**.<br>Ideal for teams using **HashiCorp Terraform** to manage cluster deployments or CI/CD pipelines. |
| 🛡️ **Install in gVisor on Kubernetes** | **gVisor** is a sandboxing technology that provides an extra **isolation layer** between applications and the host OS. You can run the agent inside gVisor for enhanced container security. | ✅ Best for **high-security environments** where container isolation is critical.<br>Useful in **multi-tenant or compliance-driven clusters**. |

---

> 💡 **Summary:**
> - Use **Helm** for easy deployment and lifecycle management.  
> - Use **DaemonSet** for direct, lightweight node coverage.  
> - Use **Terraform** for automation and repeatable IaC deployments.  
> - Use **gVisor** when you require stronger isolation and sandboxing for workloads.


---
#### ✅ 1. Create New Access Token

- Lacework FortiCNAPP Console, go to Settings > Configuration > Agent Tokens.
- Add Name and Description.
- Select Linux as the Operating System.
- Click Save to create your new access token for Linxu agent.
* An access token can be re-used for multiple agent installations.


#### ✅ 2. Deploy Helm

```bash
helm repo add lacework https://lacework.github.io/helm-charts/
helm repo update
```

#### ✅ 3.1. Method-1. Deploy Agent Using CLI:

```bash
helm upgrade --install lacework-agent lacework/lacework-agent \
  --namespace lacework --create-namespace \
  --set laceworkConfig.accessToken=2f2abcdefghijklmnopqr \
  --set laceworkConfig.serverUrl=https://aaa-eu.lacework.net \
  --set laceworkConfig.kubernetesCluster=hkeksfrankfurt \
  --set laceworkConfig.env=poc \
# --set laceworkConfig.proxyUrl=http://proxy.example:3128 \
  --set 'tolerations[0].operator=Exists'
```


#### ✅ 3.2. Method-2. Deploy Agent Using .yaml file:

```bash
cat values.yaml 
laceworkConfig:
  serverUrl: https://aaa-eu.lacework.net
  kubernetesCluster: EKSclustername
  env: poc
# proxyUrl: http://your.proxy:3128     # <— uncomment to enable
# Keep toleration so it schedules on tainted nodes
tolerations:
  - operator: Exists
```

```bash
cat values-secrets.yaml 
laceworkConfig:
  accessToken: "2abcdefghijklmnopqr"
```

```bash
helm upgrade --install lacework-agent lacework/lacework-agent \
  -n lacework --create-namespace \
  -f values.yaml -f values-secrets.yaml
```


