


⚙️ Resources Required for EKS Audit Log Integration
| **Resource**               | **Definition / Purpose**                                                                                                                         |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| **SNS Topic**              | Used to publish notifications whenever new EKS Audit Log data is available for processing.                                                       |
| **SNS Policy**             | Allows sending notifications to the Lacework FortiCNAPP SQS queue and allows the Lacework FortiCNAPP AWS account to subscribe.                   |
| **SNS Subscription**       | Connects the SNS topic to the FortiCNAPP SQS queue. One subscription is created per EKS Audit Log integration (for each AWS account or cluster). |
| **S3 Bucket**              | Serves as the storage destination for all EKS Audit Logs that FortiCNAPP ingests for continuous monitoring and analysis.                         |
| **Kinesis Data Firehose**  | Provides a continuous, managed delivery stream that transfers EKS Audit Logs from CloudWatch to the S3 bucket.                                   |
| **Firehose Delivery Role** | Grants Kinesis Firehose permission to write (post) data to the S3 bucket, ensuring secure and reliable delivery.                                 |
| **Cross-Account IAM Role** | Created in the customer’s AWS account and assumed by FortiCNAPP to make API calls, read S3 logs, and process notifications securely.             |


# 🧭 EKS → FortiCNAPP Integration Flow

|| **Step** | **Flow Description**                                                                                               | **AWS / FortiCNAPP Component**             |
| -------- | ------------------------------------------------------------------------------------------------------------------ | ------------------------------------------ |
| **1.**   | EKS control plane generates audit and activity events.                                                             | 🟧 **Amazon EKS Cloud**                    |
| **2.**   | CloudWatch collects EKS control plane events for monitoring and log aggregation.                                   | ☁️ **Amazon CloudWatch**                   |
| **3.**   | CloudWatch can’t directly export logs to S3 in real-time — so logs are streamed continuously via Kinesis Firehose. | 🔥 **Amazon Kinesis Firehose**             |
| **4.**   | Firehose buffers, compresses, and reliably delivers log data to S3 for durable storage.                            | 🪣 **Amazon S3 (EKS Logs Bucket)**         |
| **5.**   | S3 triggers notifications when new logs are written, using SNS to broadcast these events.                          | 📨 **Amazon SNS**                          |
| **6.**   | SNS delivers notifications to SQS, which queues messages for reliable, event-driven processing.                    | 📬 **Amazon SQS**                          |
| **7.**   | FortiCNAPP assumes a cross-account IAM role to securely read from SQS and access S3 logs.                          | 🪖 **FortiCNAPP Cross-Account Role (IAM)** |
| **8.**   | FortiCNAPP ingests and analyzes EKS control plane logs for compliance, visibility, and threat detection.           | 🛡️ **FortiCNAPP Platform**                |

