## 🛡️ EKS Agent Deployment Using Helm Charts

### 🧭 Goal
The goal is to have the FortiCNAPP agent running as a pod on every node in your cluster, no exceptions.
That’s why:
- You deploy it as a DaemonSet (1 pod per node)
- You add tolerations so it runs on all nodes (even tainted ones)
- You use Helm to automate and manage this deployment safely

🧠 Note: What Are “Taints” and “Tolerations” in Kubernetes?
 --set 'tolerations[0].operator=Exists' (“This agent pod tolerates all taints — allow it to run on any node.”) // Easy for POC deployments.
You can also add Explicit tolerations if customer require = "Run the agent only where I allow it"
Kubernetes taints and tolerations work together to control which Pods can run on which nodes.
Taint = rule on a node saying:
“Only pods that tolerate this taint are allowed here.”
Toleration = permission on a pod saying:
“I can run on nodes that have this taint.

- Use **Helm** for easy deployment and lifecycle management


### 🧱 Helm Summary

| **Component / Concept** | **Description** |
|--------------------------|-----------------|
| 🧩 **Helm** | A **packaging and lifecycle management tool** for Kubernetes. It can deploy and manage multiple resources such as **DaemonSets**, **Deployments**, **Services**, **ConfigMaps**, and more. |
| ⚙️ **DaemonSet** | A **Kubernetes resource** that ensures **one pod runs on every node** in the cluster. Commonly used for agents that monitor or protect each node (like the **Lacework FortiCNAPP Agent**). |
| 🔁 **How They Work Together** | **Helm → installs and manages → DaemonSet → runs agents on every node.** <br>Helm acts as the installer and updater; the DaemonSet handles actual workload placement. |
| 📦 **Helm Chart Structure** | A Helm chart is a **folder** that contains all YAML templates and configuration files needed to deploy an application on Kubernetes. It typically includes `Chart.yaml`, `values.yaml`, and a `/templates/` directory with resource definitions. |
| 🚫 **Note (Fargate Limitation)** | **AWS Fargate does not support DaemonSets.** <br>To monitor workloads on Fargate, you must either: <br>• **Embed the agent** in the container image at build time, or <br>• **Inject a sidecar** container into each pod. |

---

```bash
EKS Agent Deployment Using Helm Charts Agenda:
├ 1. Create New Agent Access Token. 
├ 2. Deploy Helm.
├ 3.1. Method-1. Deploy Agent Using CLI
├ 3.2. Method-2. Deploy Agent Using values.yaml file:
├ 4. Verification Commands
```
---
#### ✅ 1. Create New Access Token

- Lacework FortiCNAPP Console, go to Settings > Configuration > Agent Tokens.
- Add Name and Description.
- Select Linux as the Operating System.
- Click Save to create your new access token for Linxu agent.
* An access token can be re-used for multiple agent installations.


#### ✅ 2. Deploy Helm: from AWS CloudShell or Host connectected to EKS:

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

To set multiple tolerations for the Lacework FortiCNAPP agent, set an array of desired tolerations in one of the following ways:



#### ✅ 3.2. Method-2. Deploy Agent Using values.yaml file:
The values.yaml file is the configuration file for a Helm chart.
It defines default settings and parameters used by the chart’s templates — such as image names, resource limits, namespaces, labels, and other variables

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

Additonal points:
To set multiple tolerations for the  FortiCNAPP agent if not accepted to run the  FortiCNAPP agent on all nodes in your cluster:
```bash
tolerations:
# Allow Lacework agent to run on all nodes in case of a taint tolerations:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
  - effect: NoSchedule
    key: node-role.kubernetes.io/control-plane
  - effect: NoSchedule
    key: node-role.kubernetes.io/infra
```



#### ✅ 4.  🧪 Verification Commands

| **Command** | **Purpose** | **Example Output / Notes** |
|--------------|-------------|-----------------------------|
| `kubectl get pods -n lacework -o wide` | Verify that all Lacework agent pods are running and assigned to the correct nodes. | Displays pod names, IPs, and node assignments for quick validation. |
| `kubectl get daemonsets -n lacework` | Check the DaemonSet’s desired, current, and ready pod counts. | Ensures a pod has been scheduled on every node. |
| `kubectl describe ds lacework-agent -n lacework` | Get detailed info about the DaemonSet, including events and scheduling. | Useful for diagnosing pod placement or image issues. |
| `helm list -n lacework` | Verify the Helm release is installed and deployed correctly. | Lists Helm releases, chart versions, and deployment timestamps. |
| `helm status lacework-agent -n lacework` | Check the status of the Helm release and view deployed resources. | Confirms the Helm-managed DaemonSet and related resources are healthy. |
| `helm get manifest lacework-agent -n lacework \| grep -n "kind: DaemonSet"` | Confirm that the Helm chart deployed a DaemonSet resource. | Ensures the Helm release actually created the DaemonSet. |
| `kubectl logs -n lacework <pod-name>` | View logs for a specific Lacework agent pod. | Helps verify that the agent is communicating with the Lacework platform. |
| `kubectl get nodes -o wide` | Check node details and ensure each node has an agent pod assigned. | Combine with `get pods` for node-to-pod coverage verification. |
| `kubectl get daemonsets,pods -n lacework -o wide` | Quick overview of both DaemonSets and pods in the same command. | Best for a one-line cluster health snapshot. |

---

> 💡 **Tip:**  
> Use the combined command below for a fast overview of your **Lacework DaemonSet** and all associated **pods** :

```bash
kubectl get daemonsets,pods -n lacework -o wide




Installing Linux agent on Kubernetes
https://docs.fortinet.com/document/forticnapp/latest/administration-guide/663510#install-using-lacework-charts-repository-recommended


Required connectivity, proxies, and certificates for agents
https://docs.fortinet.com/document/forticnapp/latest/administration-guide/59862/required-connectivity-proxies-and-certificates-for-agents


