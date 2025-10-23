# 🧠 FortiCNAPP EKS Audit Anomaly Detection

FortiCNAPP enhances Kubernetes security visibility by extending anomaly detection and behavioral analytics into Amazon EKS clusters.
By integrating with AWS-managed EKS Audit Logs, FortiCNAPP continuously monitors and detects unusual activity, risky configurations, and potential threats across Kubernetes environments.
This feature provides deep insights, full audit visibility, and automated anomaly detection powered by machine learning and User & Entity Behavior Analytics (UEBA).


Audit logging must be enabled on the EKS clusters that you want to integrate with.



Enable via UI:
EKS --> Clusters --> Cluster-name ---> Observability ---> Control Plane Logs --> Manage --> Enable "Audit" 

Enable via CLI:
aws eks --region <region> update-cluster-config --name <cluster_name> \ --logging '{"clusterLogging":[{"types":["audit"],"enabled":true}]}'

Example:CLI
aws eks --region eu-central-1 update-cluster-config --name hkeksfrankfurt --logging '{"clusterLogging":[{"types":["audit"],"enabled":true}]}'


Option-1: Using UI and Lacework Script (Copy paste on Cloudshell or Host)
- Settings --> Cloud Accounts --> Add New --> AWS --> Guided Configuration, Next:
- Choose integration type --> EKS Audit Log: 
- Choose AWS region,  list of EKS clusters (names), & API Key
- Press Generate CLI Bundle (choose Cloudshell or supported host)
- Copy, paste the command.

Option-2: Deployment via CLI ( Host or Cloudshell): 
lacework generate k8s eks
▸ Previous IaC generation detected, load cached values? No
▸ Integrate clusters in more than one region? No
▸ Specify AWS region: eu-central-1
▸ Specify a comma-seperated list of clusters in region to ingest EKS Audit Logs: hkeksfrankfurt
▸ Configure advanced integration options? No
▸ Run Terraform plan now? (y/N) yes



## ⚙️ Resources Required for EKS Audit Log Integration
| **Resource**               | **Definition / Purpose**                                                                                                                         |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| **SNS Topic**              | Used to publish notifications whenever new EKS Audit Log data is available for processing.                                                       |
| **SNS Policy**             | Allows sending notifications to the Lacework FortiCNAPP SQS queue and allows the Lacework FortiCNAPP AWS account to subscribe.                   |
| **SNS Subscription**       | Connects the SNS topic to the FortiCNAPP SQS queue. One subscription is created per EKS Audit Log integration (for each AWS account or cluster). |
| **S3 Bucket**              | Serves as the storage destination for all EKS Audit Logs that FortiCNAPP ingests for continuous monitoring and analysis.                         |
| **Kinesis Data Firehose**  | Provides a continuous, managed delivery stream that transfers EKS Audit Logs from CloudWatch to the S3 bucket.                                   |
| **Firehose Delivery Role** | Grants Kinesis Firehose permission to write (post) data to the S3 bucket, ensuring secure and reliable delivery.                                 |
| **Cross-Account IAM Role** | Created in the customer’s AWS account and assumed by FortiCNAPP to make API calls, read S3 logs, and process notifications securely.             |


## 🧭 EKS → FortiCNAPP Integration Flow

| **Step** | **Flow Description**                                                                                               | **AWS / FortiCNAPP Component**             |
| -------- | ------------------------------------------------------------------------------------------------------------------ | ------------------------------------------ |
| **1.**   | EKS control plane generates audit and activity events.                                                             | 🟧 **Amazon EKS Cloud**                    |
| **2.**   | CloudWatch collects EKS control plane events for monitoring and log aggregation.                                   | ☁️ **Amazon CloudWatch**                   |
| **3.**   | CloudWatch can’t directly export logs to S3 in real-time — so logs are streamed continuously via Kinesis Firehose. | 🔥 **Amazon Kinesis Firehose**             |
| **4.**   | Firehose buffers, compresses, and reliably delivers log data to S3 for durable storage.                            | 🪣 **Amazon S3 (EKS Logs Bucket)**         |
| **5.**   | S3 triggers notifications when new logs are written, using SNS to broadcast these events.                          | 📨 **Amazon SNS**                          |
| **6.**   | SNS delivers notifications to SQS, which queues messages for reliable, event-driven processing.                    | 📬 **Amazon SQS**                          |
| **7.**   | FortiCNAPP assumes a cross-account IAM role to securely read from SQS and access S3 logs.                          | 🪖 **FortiCNAPP Cross-Account Role (IAM)** |
| **8.**   | FortiCNAPP ingests and analyzes EKS control plane logs for compliance, visibility, and threat detection.           | 🛡️ **FortiCNAPP Platform**                 |

Reference:

Amazon EKS Audit Log Integration
https://docs.fortinet.com/document/forticnapp/latest/administration-guide/842670/amazon-eks-audit-log-integration




