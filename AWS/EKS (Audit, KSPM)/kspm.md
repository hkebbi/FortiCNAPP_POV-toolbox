# 🧩 AWS-EKS Security Posture Management (KSPM)  Integration  

## ⚙️ Resources Required for EKS KSPM  Integration

| **KSPM Components**         | **Purpose / Function**                                                                                         | **Deployment Method**                                                 | **Privileges / Network Access**                                                                              | **Collection Frequency**        | **Data Sent to FortiCNAPP**             | **Key Requirements / Notes**                                                                                |
| --------------------- | -------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ | ------------------------------- | --------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| **Cloud Collector**   | Enumerates and assesses EKS clusters using existing **CSPM (Cloud Configuration Integration)** for compliance. | Automatically available once AWS Configuration Integration is set up. | Runs as part of the cloud integration (no pod deployment required).                                          | Daily (based on CSPM schedule). | Within 24 hours of configuration setup. | Requires AWS  Configuration Integration. No additional setup for EKS.                                 |
| **Node Collector**    | Collects **node-level data** (configurations, workloads, metadata) from each node in the EKS cluster.          | **Helm or Terraform** (not DaemonSet).                                | Runs as a **privileged pod** using the **host network namespace**.                                           | Every hour.                     | Within 2 hours of installation.         | Requires access to the **Instance Metadata Service (IMDS)**. Must be deployed on each cluster.              |
| **Cluster Collector** | Collects **cluster-wide configuration and API data** (RBAC, resources, policies).                              | **Helm or Terraform**                                                 | Runs as a **non-privileged pod** using the **pod network namespace** (can use host network if IMDS blocked). | Every 24 hours.                 | Within 2 hours of installation.         | Requires access to both the **Kubernetes API Server** and **IMDS**. If IMDS blocked → *Partial Collection*. |  

------
------

## ⚙️Configuration

In KSPM-only, only the Cluster Collector Deployment runs — not a DaemonSet per node.
That means there’s just one pod, not one per node
The tolerations (CriticalAddonsOnly, NoSchedule) are used to allow scheduling even on “critical” or tainted nodes — helpful in production clusters where scheduling restrictions might block system pods.


**Check Deployed Pods: and Cluster and Agent :**


**Deploy EKS KSPM + Agent: Cluster Collector + Node Collector :**

```bash
helm upgrade --install --create-namespace --namespace lacework \
  --set laceworkConfig.serverUrl=https://api.fra.lacework.net \
  --set laceworkConfig.accessToken=0f28b668xxxxxx9bcbe1f4ea1 \
  --set laceworkConfig.kubernetesCluster=eksfrankfurt \
  --set laceworkConfig.env=Production \
# --set laceworkConfig.proxyUrl=http://proxy.example:3128 \
  --set clusterAgent.enable=True \
  --set clusterAgent.clusterType=eks \
  --set clusterAgent.clusterRegion=eu-central-1 \
  --set clusterAgent.image.repository=lacework/k8scollector \
  --set image.repository=lacework/datacollector \
  --set "tolerations[0].key=CriticalAddonsOnly" \
  --set "tolerations[0].operator=Exists" \
  --set "tolerations[0].effect=NoSchedule" \
  --repo https://lacework.github.io/helm-charts/ \
  lacework-agent lacework-agent
```

| Variable                                            | Description                                                                                      |
| --------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| `serverUrl=${LACEWORK_SERVER_URL}`                  | FortiCNAPP (Lacework) API endpoint URL — region-specific (e.g., `https://api.fra.lacework.net`). |
| `accessToken=${LACEWORK_AGENT_TOKEN}`               | Access token used to authenticate the deployment with your FortiCNAPP tenant.                    |
| `kubernetesCluster=${KUBERNETES_CLUSTER_NAME}`      | Logical name of your Kubernetes or EKS cluster as it should appear in the FortiCNAPP console.    |
| `laceworkConfig.env=${KUBERNETES_ENVIRONMENT_NAME}` | Environment label for grouping clusters (e.g., `Production`, `Staging`).                         |

| Region                            | Server URL                            | Port        | Description                                                                  | Reference                                                                                                                                                    |
| --------------------------------- | ------------------------------------- | ----------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **United States (US)**            | `https://api.lacework.net`            | **TCP/443** | Default endpoint for **US-based FortiCNAPP/Lacework accounts**.              | [FortiCNAPP Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/59862/required-connectivity-proxies-and-certificates-for-agents) |
| **Europe (EU)**                   | `https://api.fra.lacework.net`        | **TCP/443** | Endpoint for deployments in the **European region** (Frankfurt data center). | [FortiCNAPP Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/59862/required-connectivity-proxies-and-certificates-for-agents) |
| **Australia / New Zealand (ANZ)** | `https://auprodn1.agent.lacework.net` | **TCP/443** | Endpoint for deployments in **Australia** or **New Zealand**.                | [FortiCNAPP Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/59862/required-connectivity-proxies-and-certificates-for-agents) |
| **Asia (Singapore)**              | `https://aprods1.agent.lacework.net`  | **TCP/443** | Endpoint for deployments in the **Asia region** (Singapore data center).     | [FortiCNAPP Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/59862/required-connectivity-proxies-and-certificates-for-agents) |


-----

**# If needed (IMDS Access blocked): Persist the IMDS Access fix: hostNetwork + ClusterFirstWithHostNetr :**
```bash
kubectl -n lacework patch deploy lacework-agent-cluster --type=json -p='[
  {"op":"add","path":"/spec/template/spec/hostNetwork","value":true},
  {"op":"add","path":"/spec/template/spec/dnsPolicy","value":"ClusterFirstWithHostNet"}
]'
```

**# Restart to pick it up :**
```bash
kubectl -n lacework rollout restart deploy/lacework-agent-cluster
```

**Check Deployed Pods: and Cluster and Agent :**
```bash
kubectl get pods -o wide -n lacework                                

NAME                                      READY   STATUS    RESTARTS   AGE   IP              NODE                  NOMINATED NODE   READINESS GATES
lacework-agent-7zw26                      1/1     Running   0          46s   172.31.34.187   i-0c0fa636d2fbd7808   <none>           <none>
lacework-agent-cluster-679d6c67bf-bbsqh   1/1     Running   0          43s   172.31.11.36    i-0c78ac265bfc9d185   <none>           <none>
lacework-agent-kd6vc                      1/1     Running   0          46s   172.31.15.62    i-08b86b7b9ee759e7a   <none>           <none>
lacework-agent-vc869                      1/1     Running   0          26s   172.31.11.36    i-0c78ac265bfc9d185   <none>           <none>
```

**Remove / Uninstall any Helm releases in the Lacework namespace:**
```bash
helm ls -n lacework -q | xargs -r -I{} helm uninstall {} -n lacework
```



## 🛠️ FortiCNAPP EKS KSPM References:

| Topic                                   | Description                                                                           | Reference Link                                                                                                                                                           |
| --------------------------------------- | ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Kubernetes Compliance**               | Overview of FortiCNAPP Kubernetes Compliance capabilities and configuration.          | [Kubernetes Compliance Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/462341/kubernetes-compliance)                                     |
| **FortiCNAPP EKS KSPM Troubleshooting** | Troubleshooting guide for EKS KSPM integrations, IMDS issues, and collection errors.  | [EKS KSPM Troubleshooting](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/498574/kubernetes-troubleshooting)                                  |
| **Kubernetes Compliance FAQs**          | Frequently asked questions about KSPM collectors, configuration, and data collection. | [Kubernetes Compliance FAQs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/219103/kubernetes-compliance-faqs)                                |
| **Helm Format Deployment**              | Official Helm-based deployment steps using the Lacework charts repository.            | [Helm Deployment Guide](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/663510#install-using-lacework-charts-repository-recommended)           |
| **Required Connectivity**               | Network connectivity, proxy, and certificate requirements for agents and collectors.  | [Connectivity & Certificates](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/59862/required-connectivity-proxies-and-certificates-for-agents) |

-----
-----

