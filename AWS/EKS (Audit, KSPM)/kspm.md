



| **KSPM Components**         | **Purpose / Function**                                                                                         | **Deployment Method**                                                 | **Privileges / Network Access**                                                                              | **Collection Frequency**        | **Data Sent to FortiCNAPP**             | **Key Requirements / Notes**                                                                                |
| --------------------- | -------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ | ------------------------------- | --------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| **Cloud Collector**   | Enumerates and assesses EKS clusters using existing **CSPM (Cloud Configuration Integration)** for compliance. | Automatically available once AWS Configuration Integration is set up. | Runs as part of the cloud integration (no pod deployment required).                                          | Daily (based on CSPM schedule). | Within 24 hours of configuration setup. | Requires AWS  Configuration Integration. No additional setup for EKS.                                 |
| **Node Collector**    | Collects **node-level data** (configurations, workloads, metadata) from each node in the EKS cluster.          | **Helm or Terraform** (not DaemonSet).                                | Runs as a **privileged pod** using the **host network namespace**.                                           | Every hour.                     | Within 2 hours of installation.         | Requires access to the **Instance Metadata Service (IMDS)**. Must be deployed on each cluster.              |
| **Cluster Collector** | Collects **cluster-wide configuration and API data** (RBAC, resources, policies).                              | **Helm or Terraform**                                                 | Runs as a **non-privileged pod** using the **pod network namespace** (can use host network if IMDS blocked). | Every 24 hours.                 | Within 2 hours of installation.         | Requires access to both the **Kubernetes API Server** and **IMDS**. If IMDS blocked → *Partial Collection*. |  

------
------

| **Notes**           | **Description**                                                                                                                                                                                                                                                                                                                                                                                               |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Full Collection**    | All three collectors (**Cloud**, **Node**, **Cluster**) are installed and actively sending data — complete Kubernetes compliance visibility.                                                                                                                                                                                                                                                                  |
| **Partial Collection** | One or more collectors (usually Node or Cluster) are missing or misconfigured — limited compliance visibility in FortiCNAPP.                                                                                                                                                                                                                                                                                  |
| **No Collection**      | Cloud Configuration Integration (CSPM) not set up — FortiCNAPP cannot detect or enumerate EKS clusters.                                                                                                                                                                                                                                                                                                       |
| **Common Issue**       | ⚠️ **Error Message:** `Partial collection available. The node collector has not been configured.`<br><br>**Cause:** This occurs when the `lacework-agent-cluster` pod cannot reach the **AWS Instance Metadata Service (IMDS)**.<br><br>**Resolution:** Ensure IMDS access is allowed from pods. Verify that network policies, service meshes, or firewalls are not blocking access to the metadata endpoint. |  

------

To verify IMDS access, run:
```bash
kubectl -n lacework exec deploy/lacework-agent-cluster -- sh -lc 'T=$(curl -s --connect-timeout 2 -X PUT http://169.254.169.254/latest/api/token -H "X-aws-ec2-metadata-token-ttl-seconds:60" || true); [ -n "$T" ] && { printf "instance_id: "; curl -s --connect-timeout 2 -H "X-aws-ec2-metadata-token: $T" http://169.254.169.254/latest/meta-data/instance-id || echo ERR; } || echo TOKEN_FAIL'
```
✅ If the output shows something like: instance_id: i-xxxxxxxxxxxx, IMDS access is working correctly.  
⚠️ If you see TOKEN_FAIL or ERR, IMDS is blocked — check NetworkPolicies, CNI rules, or firewall settings that might restrict access to 169.254.169.254.  
   --> Then Enables IMDS access for the lacework-agent-cluster pod:

```bash
kubectl -n lacework patch deploy lacework-agent-cluster --type=json -p='[
  {"op":"add","path":"/spec/template/spec/hostNetwork","value":true},
  {"op":"add","path":"/spec/template/spec/dnsPolicy","value":"ClusterFirstWithHostNet"}
]'
kubectl -n lacework rollout restart deploy/lacework-agent-cluster
```
**hostNetwork**: true makes the pod use the node’s network namespace, so calls to 169.254.169.254 (IMDS) go out exactly like they would from the node.


   



-----

