# 🧩 AWS-EKS Security Posture Management (KSPM)  Integration  


| **Component / Feature**    | **Description**                                                                                                                                                                                                                                |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Core Components**        | KSPM is comprised of three main collectors: **Cloud Collector**, **Node Collector**, and **Cluster Collector**. Together, they continuously monitor, assess, and secure your Kubernetes clusters.                                              |
| **Cloud Integration**      | Seamlessly integrates with **Amazon EKS** and **Google GKE** to analyze cluster configurations, workloads, and security posture.                                                                                                               |
| **Supported Environments** | **Amazon Elastic Kubernetes Service (EKS)** — supported.<br>❌ **EKS Fargate** is *not supported* for this integration.<br>**Google Kubernetes Engine (GKE)** — supported.<br>❌ **GKE Autopilot mode** is *not supported* for this integration. |
| **Resource Visibility**    | Provides a unified **Resource Inventory** showing all Kubernetes resources and their compliance state.                                                                                                                                         |
| **Compliance Monitoring**  | Offers a **Kubernetes Compliance Dashboard** for real-time cluster and resource compliance tracking.                                                                                                                                           |
| **Industry Benchmarks**    | Supports leading compliance frameworks:<br>• CIS Amazon EKS 1.1.0 Benchmark<br>• CIS Google GKE 1.4.0 Benchmark                                                                                                                                |
| **Attack Path Analysis**   | Visualizes **potential service attack paths** and relationships between workloads to detect risks and lateral movement.                                                                                                                        |
| **Continuous Monitoring**  | Automatically identifies and reports misconfigurations, policy violations, and risky Kubernetes deployments.                                                                                                                                   |
| **Outcome**                | Enables **end-to-end Kubernetes visibility**, strengthens compliance, and proactively reduces risk within FortiCNAPP.                                                                                                                      
        

------------

## ⚙️ Resources Required for EKS KSPM  Integration

| **KSPM Components**         | **Purpose / Function**                                                                                         | **Deployment Method**                                                 | **Privileges / Network Access**                                                                              | **Collection Frequency**        | **Data Sent to FortiCNAPP**             | **Key Requirements / Notes**                                                                                |
| --------------------- | -------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ | ------------------------------- | --------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| **Cloud Collector**   | Enumerates and assesses EKS clusters using existing **CSPM (Cloud Configuration Integration)** for compliance. | Automatically available once AWS Configuration Integration is set up. | Runs as part of the cloud integration (no pod deployment required).                                          | Daily (based on CSPM schedule). | Within 24 hours of configuration setup. | Requires AWS  Configuration Integration. No additional setup for EKS.                                 |
| **Node Collector**    | Collects **node-level data** (configurations, workloads, metadata) from each node in the EKS cluster.          | **Helm or Terraform** (not DaemonSet).                                | Runs as a **privileged pod** using the **host network namespace**.                                           | Every hour.                     | Within 2 hours of installation.         | Requires access to the **Instance Metadata Service (IMDS)**. Must be deployed on each cluster.              |
| **Cluster Collector** | Collects **cluster-wide configuration and API data** (RBAC, resources, policies).                              | **Helm or Terraform**                                                 | Runs as a **non-privileged pod** using the **pod network namespace** (can use host network if IMDS blocked). | Every 24 hours.                 | Within 2 hours of installation.         | Requires access to both the **Kubernetes API Server** and **IMDS**. If IMDS blocked → *Partial Collection*. |  

------------
------------

## ⚙️Configuration

In KSPM-only, only the Cluster Collector Deployment runs — not a DaemonSet per node.
That means there’s just one pod, not one per node
The tolerations (CriticalAddonsOnly, NoSchedule) are used to allow scheduling even on “critical” or tainted nodes — helpful in production clusters where scheduling restrictions might block system pods.


**Check Deployed Pods: and Cluster and Agent :**


**Deploy EKS KSPM + Agent: Cluster Collector + Node Collector :**

```bash
helm repo add lacework https://lacework.github.io/helm-charts
helm repo update
```

```bash
helm upgrade --install lacework-agent lacework/lacework-agent \
  --namespace lacework --create-namespace \
  --set laceworkConfig.serverUrl=https://api.fra.lacework.net \
  --set laceworkConfig.accessToken=xxxx \
  --set laceworkConfig.kubernetesCluster=hkeksfrankfurt \
  --set laceworkConfig.env=Production \
  --set clusterAgent.enable=true \
  --set clusterAgent.hostNetworkAccess=true \
  --set clusterAgent.clusterType=eks \
  --set clusterAgent.clusterRegion=eu-central-1 \
  --set clusterAgent.image.repository=lacework/k8scollector \
  --set image.repository=lacework/datacollector \
  --set "tolerations[0].key=CriticalAddonsOnly" \
  --set "tolerations[0].operator=Exists" \
  --set "tolerations[0].effect=NoSchedule"
```
----  

| **Variable / Option**                               | **Description**                                                                                                                                                                             | **Example / Command**                                               |
| --------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| `serverUrl=${LACEWORK_SERVER_URL}`                  | FortiCNAPP (Lacework) API endpoint URL — region-specific (e.g., `https://api.fra.lacework.net`).                                                                                            | `--set laceworkConfig.serverUrl=${LACEWORK_SERVER_URL}`             |
| `accessToken=${LACEWORK_AGENT_TOKEN}`               | Access token used to authenticate the deployment with your FortiCNAPP tenant.                                                                                                               | `--set laceworkConfig.accessToken=${LACEWORK_AGENT_TOKEN}`          |
| `kubernetesCluster=${KUBERNETES_CLUSTER_NAME}`      | Logical name of your Kubernetes or EKS cluster as it should appear in the FortiCNAPP console.                                                                                               | `--set laceworkConfig.kubernetesCluster=${KUBERNETES_CLUSTER_NAME}` |
| `laceworkConfig.env=${KUBERNETES_ENVIRONMENT_NAME}` | Environment label for grouping clusters (e.g., `Production`, `Staging`).                                                                                                                    | `--set laceworkConfig.env=${KUBERNETES_ENVIRONMENT_NAME}`           |
| `laceworkConfig.proxyUrl=http://proxy.example:3128` | **Optional:** Proxy configuration for outbound communication. You can also configure this in **FortiCNAPP UI → Agent Admin settings**.                                                      | `--set laceworkConfig.proxyUrl=http://proxy.example:3128`           |
| `clusterAgent.hostNetworkAccess=true`               | Enables the **Cluster Collector** to use the host network stack for reaching the **Kubernetes API**, **IMDS**, and **FortiCNAPP APIs**. Required if pod-level network access is restricted. | `--set clusterAgent.hostNetworkAccess=true`                         |
| 🧩 **Universal Toleration (POC / Full Coverage)**   | Allows the agent to tolerate *all taints* — runs on every node.                                                                                                                             | `--set 'tolerations[0].operator=Exists'`                            |



| Region                            | Server URL                            | Port        | Description                                                                  | Reference                                                                                                                                                    |
| --------------------------------- | ------------------------------------- | ----------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **United States (US)**            | `https://api.lacework.net`            | **TCP/443** | Default endpoint for **US-based FortiCNAPP/Lacework accounts**.              | [FortiCNAPP Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/59862/required-connectivity-proxies-and-certificates-for-agents) |
| **Europe (EU)**                   | `https://api.fra.lacework.net`        | **TCP/443** | Endpoint for deployments in the **European region** (Frankfurt data center). | [FortiCNAPP Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/59862/required-connectivity-proxies-and-certificates-for-agents) |
| **Australia / New Zealand (ANZ)** | `https://auprodn1.agent.lacework.net` | **TCP/443** | Endpoint for deployments in **Australia** or **New Zealand**.                | [FortiCNAPP Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/59862/required-connectivity-proxies-and-certificates-for-agents) |
| **Asia (Singapore)**              | `https://aprods1.agent.lacework.net`  | **TCP/443** | Endpoint for deployments in the **Asia region** (Singapore data center).     | [FortiCNAPP Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/59862/required-connectivity-proxies-and-certificates-for-agents) |


-----

**#Check the Cluster Collector logs to see if any errors are seen relating to the retrieval of metadata::**  

Check the Cluster Collector logs to see if any errors are seen relating to the retrieval of metadata:  

```bash
kubectl logs lacework-agent-cluster-7c8dd4ccb9-2wh89 -n lacework | grep EC2Metadata
```

Fxi it

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
```
```bash
NAME                                      READY   STATUS    RESTARTS   AGE   IP              NODE                  NOMINATED NODE   READINESS GATES
lacework-agent-7zw26                      1/1     Running   0          46s   172.31.34.187   i-0c0fa636d2fbd7808   <none>           <none>
lacework-agent-cluster-679d6c67bf-bbsqh   1/1     Running   0          43s   172.31.11.36    i-0c78ac265bfc9d185   <none>           <none>
lacework-agent-kd6vc                      1/1     Running   0          46s   172.31.15.62    i-08b86b7b9ee759e7a   <none>           <none>
lacework-agent-vc869                      1/1     Running   0          26s   172.31.11.36    i-0c78ac265bfc9d185   <none>           <none>
```
-----

**Verify UI Results:Collection Frequency every 24 hours**


<img width="785" height="499" alt="Screenshot 2025-10-27 at 11 25 32 AM" src="https://github.com/user-attachments/assets/91c720e7-627f-4488-ba5c-5d59fabda786" />

------------
------------

**Remove / Uninstall any Helm releases in the Lacework namespace:**
```bash
helm ls -n lacework -q | xargs -r -I{} helm uninstall {} -n lacework
```

------------
------------

## 🛠️ FortiCNAPP EKS KSPM References:

| **Topic**                                    | **Description**                                                                                               | **Reference Link**                                                                                                                                                            |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Supported Environments & Prerequisites**   | Lists supported Kubernetes environments (EKS, GKE) and required setup conditions before deploying FortiCNAPP. | [Supported Environments & Prerequisites](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/705253/supported-environments-and-prerequisites)           |
| **Kubernetes Compliance Integration (Helm)** | Step-by-step guide for integrating FortiCNAPP Kubernetes Compliance using Helm charts.                        | [Kubernetes Compliance Integration using Helm](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/116902/kubernetes-compliance-integration-using-helm) |
| **Kubernetes Compliance**                    | Overview of FortiCNAPP Kubernetes Compliance capabilities and configuration.                                  | [Kubernetes Compliance Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/462341/kubernetes-compliance)                                          |
| **FortiCNAPP EKS KSPM Troubleshooting**      | Troubleshooting guide for EKS KSPM integrations, IMDS issues, and collection errors.                          | [EKS KSPM Troubleshooting](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/498574/kubernetes-troubleshooting)                                       |
| **Kubernetes Compliance FAQs**               | Frequently asked questions about KSPM collectors, configuration, and data collection.                         | [Kubernetes Compliance FAQs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/219103/kubernetes-compliance-faqs)                                     |
| **Helm Format Deployment**                   | Official Helm-based deployment steps using the Lacework charts repository.                                    | [Helm Deployment Guide](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/663510#install-using-lacework-charts-repository-recommended)                |
| **Required Connectivity**                    | Network connectivity, proxy, and certificate requirements for agents and collectors.                          | [Connectivity & Certificates](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/59862/required-connectivity-proxies-and-certificates-for-agents)      |

-----
-----

