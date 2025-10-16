# ☁️ AWS: Agentless Workload Scanning (AWLS)

## 🧠 Why AWLS?

**Agentless Workload Scanning (AWLS)** provides comprehensive visibility into **vulnerability risks** and **secrets** across your cloud workloads — without installing agents.  

This method offers **flexibility and coverage** for scanning both **hosts** and **container images**, including:
- Scanning **running containers**
- Scanning **images stored on disk**

> 🧩 *This extends the visibility beyond what the FortiCNAPP Agent provides, since direct in-place container scanning isn’t supported by the agent.*

### Key Benefits
- Gain insight into **CVEs** on hosts and containers  
- Eliminate the need to install or manage agents  
- Maintain **private-by-design** scanning in your own AWS environment  
- Improve coverage for container and host vulnerability detection  

> ⚠️ **Note:**  
> - AWLS does **not** provide workload activity monitoring.  
> - To gain full runtime visibility and behavioral analytics, you must also deploy the **FortiCNAPP Agent**.  
> - Agentless is **complementary** to the agent — designed to **co-exist**, not replace it.

---

## 🧩 What Is Deployed?

### 🛡️ Private-by-Design: Agentless Workflow

| Step | Description |
|------|--------------|
| **1** | The customer runs the **Agentless AWLS Terraform module** to deploy the required resources in their AWS environment. |
| **2** | Terraform template provisions: <br>• IAM Roles <br>• S3 Bucket <br>• ECS Cluster (with *Sidekick* container) <br>• VPC, Subnet, and Internet Gateway per Region |
| **3** | The **Sidekick container** is executed as part of an **ECS Fargate task**. |
| **4** | The task enumerates customer workloads, identifies attached block volumes, securely mounts them, and initiates the scanning process. |
| **5** | Scanning results are written to the customer’s **S3 bucket**. |
| **6** | **FortiCNAPP** reads metadata and scan results from the customer’s S3 bucket for further processing. |

---

## ⚙️ Additional Information

- 🧹 The scanner periodically removes **old snapshots** and **stale scan tasks**.  
- ⏱️ **Scan frequency:** by default, scans run **every 24 hours**.  
- 🔒 **Privacy-first:** FortiCNAPP has **no direct access** to customer cloud workloads — only to resources it deploys with **limited IAM permissions**.  
- 🧮 **Selective Scanning:** You can limit the scanned hosts using an explicit **query filter** (by default, all workloads are scanned).  
- 🐳 **Powered by ECS:**  
  > *Amazon Elastic Container Service (Amazon ECS)* is a fully managed container orchestration platform that simplifies deployment, scaling, and management of containerized applications.

---

## 🚀 How AWLS Is Deployed

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



In this setup, **Terraform** is used via the **FortiCNAPP CLI** to deploy a **multi-regional, single-account** or **multi-account** environment.

### 🔧 Configuration Flow (Enabling AWS AWLS Only)


```bash
lacework generate cloud-account aws
```

<img width="895" height="437" alt="image" src="https://github.com/user-attachments/assets/4f792cef-f653-4b2c-976f-61e831718318" />


<img width="1059" height="98" alt="Screenshot 2025-10-16 at 3 47 37 PM" src="https://github.com/user-attachments/assets/477db464-8e70-42f7-803b-cade1ac8a74a" />


## 🔗 Reference Links

| Reference | Description |
|------------|--------------|
| [**Integrating Agentless Workload Scanning for AWS Single Account with Terraform**](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/983212/integrating-agentless-workload-scanning-for-aws-single-account-with-terraform) | Step-by-step guide for deploying Agentless Workload Scanning (AWLS) on a **single AWS account** using Terraform. |
| [**Integrating Agentless Workload Scanning for AWS Organization Account with Terraform**](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/864699/integrating-agentless-workload-scanning-for-aws-organization-account-with-terraform) | Instructions for setting up AWLS for an **AWS Organization (multi-account)** environment using Terraform. |
| [**Integrating Agentless Workload Scanning with AWS using Terraform**](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/744245/terraform) | General integration overview and Terraform usage guide for **Agentless Workload Scanning** on AWS. |
| [**Terraform Module for Lacework & AWS Agentless Scanning**](https://registry.terraform.io/modules/lacework/agentless-scanning/aws/latest) | Official **Terraform Registry** module for configuring an integration between **Lacework** and **AWS** for agentless workload scanning. |

