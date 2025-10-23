EKS Audit Anomaly Detection
User Entity Behaviour Analytics (UEBA)	

FortiCNAPP extends anomaly detection ino EKS cluster. 

integrates with AWS to analyze EKS Audit Logs for monitoring EKS cluster security and configuration compliance. 
The core capabilities of the feature will focus on monitoring the AWS Managed Kubernetes Service (EKS) Audit Logs to automatically identify and analyze Kubernetes deployments for breaches, anomalous behaviors and misuse on a continuous basis.  With this feature, FortiCNAPP provides a full audit trail of all EKS activity such as the deployment of resources, errors, user activities, etc. Lacework automatically detects and alerts on unusual events such as new workloads with risky configurations, potential rogue containers from new container registries, etc. This can be complemented with policies to address custom best practices.
FortiCNAPP provides a visualization of these events with the Polygraph, including the ability to map Kubernetes users with their AWS identity.

K8s audit logs are extremely noisy, our ML-based anomaly detection reduces number of alerts

New K8s polygraph

Detections
Detect unsafe configuration of new resources

RBAC: roles with too much access, users bound to system-admin, etc.

Workloads: privileged, running as root, broad access to the host, etc.

New container registries used

New polygraphs that describe who is calling the Kubernetes API for what actions.

Track users across the cloud and Kubernetes.

Show all deployment of new resources, access to workloads, etc.

Find issues around insufficient privileges, misbehaviors, rogue containers, etc.

 Detect and audit manual changes

Deletion of a namespace and all associated objects, deletion or updates of policies (network, Pod Security, etc.)

Access to containers: kubectl exec/attach/port-forward/etc.

Alert on abnormal K8s Activities

API Errors due to bad behaviors, wrong access, etc.

Authentication misconfiguration: unauthenticated user actions, API  server open to the Internet, etc.

