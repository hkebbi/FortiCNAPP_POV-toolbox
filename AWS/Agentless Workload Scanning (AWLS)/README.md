AWS: Agentless Workload Scanning - AWLS:

Why AWLS ?  
Agentless workload scanning enables you to gain comprehensive visibility into vulnerability risks and secrets across your cloud workloads.
With this option, you have more flexibility and choice to scan and detect vulnerabilities and secrets across all hosts and container images to meet your coverage  without the need to install agents.
This gives customers visibility into CVEs present on their hosts and containers. For containers, it can scan both running containers as well as images residing on the disk,  which is a feature add to our agent, as scanning running images directly on the machine itself is not supported. 

It does not give workload activity monitoring, to get that full experience: FortiCNAPP Agent must be installed.
- Agentless is designed not to replace the Agent, but to co-exist with both adding additional value and insights. 

What is Deployed ?  
Private by design: Agentless Workload Scanning workflow

1- Customer runs Agentless AWLS Terraform to deploy resources in their cloud. (Explained in details next part)
    * Create IAM Roles, S3 Bukets, ECS clusters (with sidekick container), VPC, subnet, and Internet Gateway for the ECS cluster per Region.
2- The sidekick container is executed by an ECS Fargate task.
3- The task automates the enumeration of customer workloads, identification of attached block volumes, & secure mounting of these volumes.
4- Start execution of scanning operations to collect and analyze data from customer environment
5- Scanning task writes output to customer S3 storage.
6- FortiCNAPP read metadata from the customer’s cloud S3 bucket storage, where scanner wrote its results.
7- By default, agentless workload scanning runs every 24 hours

Additional Info:
* Periodically, the scanner removes any old snapshots and long-running scan tasks.
* By default, agentless workload scanning runs every 24 hours
* Amazon Elastic Container Service (Amazon ECS) is a fully managed container orchestration service that helps you easily deploy, manage, and scale containerized applications
* FortiCNAPP does not have access to customer cloud (only what FortiCNAPP deployed, with limited permissions)
* Optionally: Limit the scanning hosts using a query to explicitly select the scanned hosts. By default, all workloads will be scanned.

How AWLS is deployed ?

In this deployment we will use Terraform via FortiCNAPP CLI (lacework generate cloud-account aws ):  

<img width="895" height="437" alt="image" src="https://github.com/user-attachments/assets/181a3eae-b835-47c8-a7c7-5cb9abb1e45d" />




Deployment:
This module will install Global and Regional resources. 
The Global resources (Monitored account) should be installed once for the FortiCNAPP integration (Where a role used to create snapshots, and access snapshot, etc..)
The Regional resources (Scanning account) should be installed in each region where scanning will occur. Having per-region resources assures that no cross-region traffic occurs.


Once you have read and completed the Prerequisites, complete the integration steps depending on your chosen integration level:

AWS Single Account Integration
AWS Organization Integration (two options available)




https://registry.terraform.io/modules/lacework/agentless-scanning/aws/latest
<img width="451" height="336" alt="Screenshot 2025-10-15 at 11 12 24 AM" src="https://github.com/user-attachments/assets/95dbc81c-8c8e-48e2-9bd7-f38e53f85a76" />





lacework generate cloud-account aws  
<img width="654" height="319" alt="Screenshot 2025-10-15 at 12 20 44 PM" src="https://github.com/user-attachments/assets/b8b6638d-1317-439b-863a-4f02a8d6928f" />






<img width="753" height="763" alt="Screenshot 2025-10-15 at 11 16 39 AM" src="https://github.com/user-attachments/assets/2e7ed061-1e2c-4dbd-b414-e335992ab14e" />Agentless workload scanning enables you to quickly gain comprehensive visibility into vulnerability risks and secrets across your cloud workloads — without the need to install agents. With this option, you have more flexibility and choice to scan and detect vulnerabilities and secrets across all hosts and container images to meet your coverage needs. For example, you can integrate your AWS organization account with Lacework's agentless workload scanning to scan for vulnerability risks and secrets within all your cloud accounts. For maximum value and security, you can combine agentless workload scanning with agent-based workload scanning to gain deeper insights.

