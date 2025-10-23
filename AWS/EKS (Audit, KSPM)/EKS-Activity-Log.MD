









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



| Resource                              | Definition                                                                                                |
| ------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| **IAM Cross-Account Role**            | Grants Lacework cross-account access to assess AWS resource configurations and analyze CloudTrail events. |
| **Lacework Custom IAM Policy**        | Provides Lacework with read-only permissions to ingest and analyze EKS Audit Logs.                        |
| **SNS Topic**                         | Created for EKS Audit Log integrations to enable event notifications when new logs are published.         |
| **S3 Bucket**                         | Stores EKS Audit Logs for Lacework ingestion and continuous security analysis.                            |
| **S3 Bucket Notification**            | Sends notifications to the SNS topic when new log objects are created in the S3 bucket.                   |
| **S3 Bucket Lifecycle Rule**          | Defines how long logs are retained in the bucket (default retention: **180 days**).                       |
| **S3 Bucket Versioning**              | Enables version control to preserve, retrieve, and restore previous versions of log objects.              |
| **Kinesis Firehose**                  | Streams and delivers EKS Audit Logs from CloudWatch to the designated S3 bucket.                          |
| **IAM Lacework Firehose Role**        | Allows the Firehose service to assume the necessary IAM role for delivering logs.                         |
| **Lacework Firehose IAM Policy**      | Grants Firehose permissions to manage data and objects within the target S3 bucket.                       |
| **IAM Lacework CloudWatch Role**      | Enables Lacework to assume CloudWatch roles across regions for log collection.                            |
| **Lacework CloudWatch IAM Policy**    | Authorizes Firehose to access and retrieve CloudWatch logs for delivery.                                  |
| **CloudWatch Subscription Filter(s)** | Configures filters for each EKS cluster to notify when new logs are added to CloudWatch Log Groups.       |



| **Step** | **Flow Description**                                                                                                    | **AWS / FortiCNAPP Component**             |
| -------- | ----------------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| **1.**   | EKS control plane generates audit and activity events.                                                                  | 🟧 **Amazon EKS Cloud**                    |
| **2.**   | These EKS control plane events are sent to CloudWatch for logging and monitoring.                                       | ☁️ **Amazon CloudWatch**                   |
| **3.**   | CloudWatch forwards the events to Kinesis Firehose for log delivery and stream processing.                              | 🔥 **Amazon Kinesis Firehose**             |
| **4.**   | Kinesis Firehose writes the EKS Audit Logs to an S3 bucket.                                                             | 🪣 **Amazon S3 (EKS Logs Bucket)**         |
| **5.**   | The S3 bucket triggers an SNS topic when new logs (objects) are written.                                                | 📨 **Amazon SNS**                          |
| **6.**   | The SNS topic sends notifications to an SQS queue for message handling and event delivery.                              | 📬 **Amazon SQS**                          |
| **7.**   | FortiCNAPP’s cross-account IAM role assumes permissions to read from the SQS queue and access S3 logs.                  | 🪖 **FortiCNAPP Cross-Account Role (IAM)** |
| **8.**   | FortiCNAPP ingests the EKS control plane audit logs for continuous monitoring, compliance checks, and threat detection. | 🛡️ **FortiCNAPP Platform**                |



