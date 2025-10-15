Agentless Workload Scanning - AWLS:

Why ? 



How ?


What ?


What minimum CPU and memory are required for agentless scanning?
Agentless scanning does not require CPU and memory from your active workloads. It uses its own serverless cluster and configures its own CPU and memory limits that are optimized for cost savings.
- More frequent scans can result in higher AWS costs for snapshotting and periodic scanning.



By default, agentless workload scanning runs every 24 hours. This scan frequency can be configured in the integration settings in the FortiCNAPP console:


Scan results are evaluated every 24 hours and reported in the Vulnerabilities section of the Lacework FortiCNAPP console.
The evaluation frequency is fixed at a 24-hour interval and is not configurable. Changing the agentless scanning frequency will not change the evaluation frequency. When scan intervals are shortened to less than 24 hours, all the scanned manifests are saved and evaluated at the next scheduled evaluation run.



How does  FortiCNAPP scan encrypted volumes?
The scanning infrastructure runs in your account, so you can securely delegate key management privileges to the role that is invoked to run the scan.


Are agentless snapshots automatically deleted?
Periodically, the scanner removes any old snapshots and long-running scan tasks. The scanner cleans up its own resources and does not require management or cleanup by you.
The following rules apply:
Tags are applied to agentless snapshots. The installed IAM policy allows only snapshots containing these tags to be deleted.
A single snapshot per host is retained to determine if content has changed since the last snapshot.
Each hour, snapshots older than 24 hours are removed.


Agentless workload scanning logs details about any secret credentials and associated file metadata found during scans.
The files are identified as secrets if they adhere to a common format depending on the type of credential. The actual content of any secret credentials is not logged.



Deployment:
This module will install global and regional resources. 
The global resources should be installed once for a  FortiCNAPP integration. 
The regional resources should be installed in each region where scanning will occur. Having per-region resources assures that no cross-region traffic occurs.


Once you have read and completed the Prerequisites, complete the integration steps depending on your chosen integration level:

AWS Single Account Integration
AWS Organization Integration (two options available)



High-level deployment requirements
Create ECS clusters, then create a VPC, subnets, and Internet Gateway for the ECS cluster.
IAM permission to create an ECS task execution role, task role, and an EventBridge role for starting ECS tasks.
IAM permission to create a cross-account role that has permissions to read from a newly created S3 bucket and start ECS tasks.
Create CloudWatch Log Groups and Streams.
Create a new S3 bucket.
Create a new secret in AWS Secrets Manager.


- The Scanning account is the AWS account where the scanning infrastructure will be installed per region.
This includes a new VPC, Internet Gateway, and ECS Cluster per region.
- Monitored account where a role used to create snapshots, and access snapshot data, is installed.

Amazon Elastic Container Service (Amazon ECS) is a fully managed container orchestration service that helps you easily deploy, manage, and scale containerized applications.


https://registry.terraform.io/modules/lacework/agentless-scanning/aws/latest
<img width="451" height="336" alt="Screenshot 2025-10-15 at 11 12 24 AM" src="https://github.com/user-attachments/assets/95dbc81c-8c8e-48e2-9bd7-f38e53f85a76" />





lacework generate cloud-account aws  
<img width="654" height="319" alt="Screenshot 2025-10-15 at 12 20 44 PM" src="https://github.com/user-attachments/assets/b8b6638d-1317-439b-863a-4f02a8d6928f" />


Optionally: 
Limit the scanning using a query to explicitly select the scanned hosts. By default, all workloads will be scanned.



<img width="753" height="763" alt="Screenshot 2025-10-15 at 11 16 39 AM" src="https://github.com/user-attachments/assets/2e7ed061-1e2c-4dbd-b414-e335992ab14e" />Agentless workload scanning enables you to quickly gain comprehensive visibility into vulnerability risks and secrets across your cloud workloads — without the need to install agents. With this option, you have more flexibility and choice to scan and detect vulnerabilities and secrets across all hosts and container images to meet your coverage needs. For example, you can integrate your AWS organization account with Lacework's agentless workload scanning to scan for vulnerability risks and secrets within all your cloud accounts. For maximum value and security, you can combine agentless workload scanning with agent-based workload scanning to gain deeper insights.

