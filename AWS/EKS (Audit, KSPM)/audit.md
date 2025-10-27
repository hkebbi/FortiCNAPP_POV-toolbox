# 🧩 AWS-EKS Audit Integration  

## ⚙️ Definition  

| **Capability / Feature**                       | **Description**                                                                                                                                                                    |
| ---------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Enhanced Kubernetes Security Visibility**    | FortiCNAPP extends **anomaly detection** and **behavioral analytics** into **Amazon EKS clusters**, providing deep visibility into cluster activity and control-plane operations.  |
| **EKS Audit Log Integration**                  | Integrates directly with **AWS-managed EKS Audit Logs** to continuously monitor and detect **unusual activity**, **risky configurations**, and **potential threats** in real time. |
| **🔎 Deep Audit Visibility**                   | Provides comprehensive visibility into **EKS control-plane API events**, including user actions, resource modifications, and access attempts.                                      |
| **🤖 ML-Based Anomaly Detection**              | Uses **machine learning** to automatically identify **deviations from normal behavior**, reducing manual investigation time.                                                       |
| **👥 User & Entity Behavior Analytics (UEBA)** | Leverages **UEBA** to correlate and analyze user activity patterns, reducing false positives and surfacing only true security anomalies.                                           |

-----
-----

## 📋 EKS Audit Logging Requirement

| **Requirement**   | **Description / Steps**                                                                                       |
| ----------------- | ------------------------------------------------------------------------------------------------------------- |
| **Audit Logging** | Must be enabled on all EKS clusters that you plan to integrate with FortiCNAPP.                               |
| **Purpose**       | EKS Audit Logs capture control-plane API calls, enabling continuous anomaly detection and security analytics. |


| **Method**           | **Steps / Command**                                                                                                                                                      |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **AWS Console (UI)** | 1. Navigate to **EKS → Clusters → Cluster Name → Observability → Control Plane Logs → Manage**<br>2. Enable **Audit logging**<br>3. Click **Save**                       |
| **AWS CLI**          | <pre>aws eks --region <region> update-cluster-config \ <br>  --name <cluster_name> \ <br>  --logging '{"clusterLogging":[{"types":["audit"],"enabled":true}]}'</pre>     |
| **Example (CLI)**    | <pre>aws eks --region eu-central-1 update-cluster-config \ <br>  --name hkeksfrankfurt \ <br>  --logging '{"clusterLogging":[{"types":["audit"],"enabled":true}]}'</pre> |


-----
-----

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

-----
-----

## 🚀 Terraform deployment Options

| **Option**                                        | **Description / Steps**                                                                                                                                                                                                                                                                                   | **Best For**                                       |
| ------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------- |
| **Option 1 — UI-Based Integration (Recommended)** | 1. Go to **Settings → Cloud Accounts → Add New → AWS**<br>2. Choose **Integration Type → EKS Audit Log**<br>3. Select AWS region, EKS clusters, and API key<br>4. Click **Generate CLI Bundle**<br>5. Choose **CloudShell** or supported host<br>6. Copy & paste the generated command into your terminal | ✅ Simplified setup via UI and guided configuration |
| **Option 2 — CLI-Based Integration (Terraform)**  | 
<pre>lacework generate k8s eks   
▸ Integrate in multiple regions? No
▸ Specify AWS region: eu-central-1
▸ Clusters: hkeksfrankfurt
▸ Advanced options? No
▸ Run Terraform plan? yes</pre> 

| **Parameter**    | **Description**                                           |
| ---------------- | --------------------------------------------------------- |
| **AWS Region**   | Must match the region where your EKS cluster is deployed. |
| **Cluster Name** | Must match your actual EKS cluster name.                  |


-----
-----
## 🧭 UI result  
Workloads --> Kubernetes --> API tab.

<img width="1082" height="697" alt="Screenshot 2025-10-27 at 1 35 39 PM" src="https://github.com/user-attachments/assets/c71080cb-d732-43e8-9c5c-3c0a3e25bbb0" />




-----
-----
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



-----
-----


## Reference:

| **Topic**                            | **Description**                                                                                                          | **Reference Link**                                                                                                                                    |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Amazon EKS Audit Log Integration** | Official FortiCNAPP documentation detailing setup, configuration, and permissions for integrating Amazon EKS Audit Logs. | [Amazon EKS Audit Log Integration](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/842670/amazon-eks-audit-log-integration) |

