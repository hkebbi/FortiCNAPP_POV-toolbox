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
    * Template: Create IAM Roles, S3 Buket, ECS cluster(with sidekick container), VPC, subnet, and Internet Gateway for the ECS cluster per Region.
2- The sidekick container is executed by an ECS Fargate task.
3- The task automates the enumeration of customer workloads, identification of attached block volumes, & secure mounting of these volumes.
4- Start execution of scanning operations to collect and analyze data from customer environment
5- Scanning task writes output to customer S3 storage.
6- FortiCNAPP read metadata from the customer’s cloud S3 bucket storage, where scanner wrote its results.

Additional Info:
* Periodically, the scanner removes any old snapshots and long-running scan tasks.
* By default, agentless workload scanning runs every 24 hours.
* FortiCNAPP does not have access to customer cloud (only what FortiCNAPP deployed, with limited permissions)
* Optionally: Limit the scanning hosts using a query to explicitly select the scanned hosts. By default, all workloads will be scanned.
* Amazon Elastic Container Service (Amazon ECS) is a fully managed container orchestration service that helps you easily deploy, manage, and scale containerized applications.

How AWLS is deployed ?

In this deployment we will use Terraform via FortiCNAPP CLI for Multi-Regional Single Account (lacework generate cloud-account aws ):  
- This way can use the same for Organizational level or Multi-Account deployments. 

<img width="895" height="437" alt="image" src="https://github.com/user-attachments/assets/181a3eae-b835-47c8-a7c7-5cb9abb1e45d" />


In Summary:
Agentless is a special case, the scanning takes place on the customers own environment, we deploy terraform template which creates ECS cluster in each region that you want to scan in, it comes online and scans hosts and then sends those results to FortiCNAPP backend.
The ECS clusters are pre configured to run the agentless scanning task, the task turns on and speaks to api.lacework.net and asks for its current configuration, the interval in which it should run (default 24 hours based on when customer turns integration on) , exclusions set, feature flags set so we can enable beta features

It authenticates using a server token key, which is the same key if a customer uses an inline scanner.


Deployment:
This Terrafirm module will install Global and Regional resources. 
The Global resources (Monitored account) should be installed once for the FortiCNAPP integration (Where a role used to create snapshots, and access snapshot, etc..)
The Regional resources (Scanning account) should be installed in each region where scanning will occur. Having per-region resources assures that no cross-region traffic occurs.


## ☁️ AWS & FortiCNAPP Agentless Workload Scanning- AWLS Terraform Prerequisites

| Component / Requirement | Description | Reference / Link |
|--------------------------|--------------|------------------|
| 👩‍💻 **AWS Account Admin** | Administrative privileges on each AWS account. Required during onboarding process only. | — |
| 🧾 **AWS Service Quotas** | Ensure that your AWS account has sufficient **Service Quotas** to create and support the required resources in each selected region. This includes quotas for **ECS**, **VPC**, and **Internet Gateways**. | — |
| 🔧 **AWS CLI** | Install and configure the AWS CLI to connect to your AWS Account. | [AWS CLI Installation Guide](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) |
| 🧰 **Linux Tools** | Ensure basic tools (`curl`, `git`, `unzip`) are available in the system `PATH`. | — |
| ⚙️ **Terraform** | Install Terraform if not already configured. | [Terraform Documentation](https://developer.hashicorp.com/terraform/docs) |
| 🧠 **FortiCNAPP CLI** | Open-source CLI tool written in Golang. Available for **Linux**, **macOS**, and **Windows**. Used to interact with FortiCNAPP via the command line. | [FortiCNAPP CLI Guide](https://forticonapp.docs.fortinet.com/cli-guide) |
| ⚡ **Deployment Methods** | Supported installation environments and automation options:<br>• **AWS Cloud Shell**<br>• **Hosts Supported by Terraform** | — |




AWS Single Account Integration
AWS Organization Integration (two options available)

 AWS account must have Service Quotas allowing  these resources to be created and supported in each region selected: 
 - ECS, VPC, Internet Gateways







Scanning account: Scanning infrastructure will only be installed on this account. This includes a new VPC, Internet Gateway and ECS Cluster per region.
Monitored accounts: A role is installed that will create snapshots and access snapshot data.
Reference:

Terraform module for configuring an integration with Lacework and AWS for agentless scanning: 
https://registry.terraform.io/modules/lacework/agentless-scanning/aws/latest

