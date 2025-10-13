## 🛡️ EKS Agent Deployment Using Helm Charts
- Use **Helm** for easy deployment and lifecycle management

### 🧭 Helm Summary

| **Component / Concept** | **Description** |
|--------------------------|-----------------|
| 🧩 **Helm** | A **packaging and lifecycle management tool** for Kubernetes. It can deploy and manage multiple resources such as **DaemonSets**, **Deployments**, **Services**, **ConfigMaps**, and more. |
| ⚙️ **DaemonSet** | A **Kubernetes resource** that ensures **one pod runs on every node** in the cluster. Commonly used for agents that monitor or protect each node (like the **Lacework FortiCNAPP Agent**). |
| 🔁 **How They Work Together** | **Helm → installs and manages → DaemonSet → runs agents on every node.** <br>Helm acts as the installer and updater; the DaemonSet handles actual workload placement. |
| 📦 **Helm Chart Structure** | A Helm chart is a **folder** that contains all YAML templates and configuration files needed to deploy an application on Kubernetes. It typically includes `Chart.yaml`, `values.yaml`, and a `/templates/` directory with resource definitions. |
| 🚫 **Note (Fargate Limitation)** | **AWS Fargate does not support DaemonSets.** <br>To monitor workloads on Fargate, you must either: <br>• **Embed the agent** in the container image at build time, or <br>• **Inject a sidecar** container into each pod. |

---



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


### 🧪 Verification Commands

| **Command** | **Purpose** | **Example Output / Notes** |
|--------------|-------------|-----------------------------|
| `kubectl get pods -n lacework -o wide` | Verify that all Lacework agent pods are running and on the correct nodes. | Shows pod names, node assignments, and IPs for quick status checks. |
| `kubectl get daemonsets -n lacework` | Check the DaemonSet status (desired vs. current pods). | Confirms that a pod has been scheduled on every node. |
| `kubectl describe ds lacework-agent -n lacework` | Get detailed information about the DaemonSet, including events and scheduling. | Useful for troubleshooting scheduling or image issues. |
| `helm list -n lacework` | Verify that the Helm release is installed and deployed correctly. | Displays Helm releases, versions, and deployment timestamps. |
| `helm status lacework-agent -n lacework` | Get a summary of the Helm release status and resources created. | Confirms if the Helm-managed DaemonSet and related resources are healthy. |
| `helm get manifest lacework-agent -n lacework | grep -n "kind: DaemonSet` | Confirm that the Helm chart deployed a DaemonSet resource. | Ensures the chart actually creates a DaemonSet during installation. |
| `kubectl logs -n lacework <pod-name>` | View the logs of a specific Lacework agent pod. | Helps verify that the agent is successfully connecting and sending telemetry. |
| `kubectl get nodes -o wide` | Cross-check which nodes exist and ensure each has a Lacework pod scheduled. | Use this along with `get pods` to verify node-to-pod mapping. |

---

> 💡 **Tip:**  
> After installation, always verify the **DaemonSet** and **pods** are in a `READY` state across all nodes.  
> You can combine commands like:  
> ```bash
> kubectl get daemonsets,pods -n lacework -o wide
> ```  
> to see both resources together for a quick health snapshot.


