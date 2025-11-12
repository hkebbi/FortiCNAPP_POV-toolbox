# ☁️ AWS: Cloud API Integration

## Note: Make sure to review the following files before proceeding.

- [AWS CloudShell Size Workaround](../cloudshell-size-workaround.md)
- [FortiCNAPP & AWS Profiles](../forticnapp-aws-profile.md)
- [AWS Terms and Architecture](../aws-terms-arch.md)

-----
-----

## 🧾 Onboarding Requirements (Just for deployment phase - Terraform) 

| **Component / Requirement** | **Description** | **Reference / Link** |
|------------------------------|-----------------|----------------------|
| 🧑‍💻 **AWS Account Admin** | Administrative privileges on each AWS account.<br>Required during onboarding process only. | — |
| **Lacework FortiCNAPP Administrator Account** | A Lacework account with **administrator privileges** to create and manage cloud integrations. To be Registered on FortiCloud prior to POV| 
| ⚡ **Deployment Methods** | Supported installation environments and automation options:<br>• **AWS Cloud Shell**<br>• **Hosts Supported by Terraform connect to AWS Account** | — |

-----
-----

## 🧠 Why Cloud API Integration ?

Integrates with your AWS Cloud API environment to have the visibiltiy for Configuration Compliance, Cloud Identity risk managemen & and Threat Detection across your cloud environment.
FortiCNAPP connects to AWS through a secure cross-account role to collect **configuration**, **identity**, and **activity** data.  
The following integrations — **CSPM**, **CloudTrail**, and **CIEM** — work together to deliver unified visibility and risk analysis across your AWS environment.

### 🧩 Cloud Security Posture Management (CSPM) — Configuration Workflow

| **Aspect** | **Description** |
|-------------|-----------------|
| **Purpose** | Provides visibility into cloud misconfigurations, security risks, and compliance posture across AWS resources. |
| **Data Source** | IAM Role  Get,List for: AWS Config, EC2, S3, SNS, SQS. |
| **Workflow** | FortiCNAPP uses the IAM role to **read resource configurations** and analyze posture against security best practices and compliance frameworks (CIS, NIST, PCI-DSS, ISO). |
| **Findings** | Detects issues like public S3 buckets, open security groups, disabled encryption, unused keys, and noncompliant configurations. |
| **Outcome** | Continuous configuration visibility and automated compliance posture scoring per AWS account and region. |

---
### 📜 CloudTrail Integration — Configuration Workflow

| **Aspect** | **Description** |
|-------------|-----------------|
| **Purpose** | Collects and analyzes AWS account activity for anomaly detection, behavioral analysis, and forensic visibility. |
| **Data Source** | AWS CloudTrail + IAM Role Get,List for: S3, SNS, SQS, CloudTrail + Cross-Account Lacework IAM Role Get,List to poll and analyze CloudTrail events |
| **Workflow** | CloudTrail delivers logs to S3 → triggers SNS → sends messages to SQS → FortiCNAPP polls SQS to read new log file details  |
| **Findings** | Detects suspicious API calls, unauthorized changes, and unusual activity patterns in AWS accounts. |
| **Outcome** | Provides a real-time feed of AWS API activity correlated with configuration and identity data for deep event-based analysis. |

> 💡 **Note:**

> Ensure that your cross-account IAM role and S3 bucket are collocated in the same AWS account.
 
> SNS publishes event notifications (for example, new CloudTrail logs in S3).

> SQS subscribes to the SNS topic and stores messages reliably.

> FortiCNAPP polls SQS at its own pace, ensuring no events are lost.

> Once processed, messages are deleted from SQS.

---

### 🔐 Cloud Infrastructure Entitlement Management (CIEM) — Configuration Workflow

| **Aspect** | **Description** |
|-------------|-----------------|
| **Purpose** | Analyzes AWS IAM users, roles, and policies to identify over-permissions, risky access, and privilege escalation paths. |
| **Data Source** | AWS IAM, AWS Organizations, AWS Config, and CloudTrail (for identity activity correlation). |
| **Workflow** | FortiCNAPP uses the IAM role to read IAM configurations and correlates that with CloudTrail data to build an **identity graph** showing relationships between users, roles, and permissions. |
| **Findings** | Detects unused roles, overly permissive policies (`*:*`), cross-account trust, orphaned users, and privilege escalation opportunities. |
| **Outcome** | Provides complete IAM visibility, access path analysis, and least-privilege recommendations to strengthen identity security posture. |

---
--- 

<img width="1140" height="611" alt="Screenshot 2025-11-12 at 3 23 11 PM" src="https://github.com/user-attachments/assets/277eeb38-2369-4851-9a66-9eea782d47f2" />

----
<img width="699" height="344" alt="Screenshot 2025-11-12 at 3 30 03 PM" src="https://github.com/user-attachments/assets/caeb6168-0c4d-49ca-ac08-7fff8bafb7d0" />

----
<img width="870" height="299" alt="Screenshot 2025-11-12 at 3 29 12 PM" src="https://github.com/user-attachments/assets/003ab21b-abaf-4c7b-aac1-ba262736f4e8" />

<img width="425" height="316" alt="Screenshot 2025-11-12 at 3 28 52 PM" src="https://github.com/user-attachments/assets/9f2e6e04-b80d-4aca-b6f7-40e9abc4ab31" />

----
----

### 🧱 FortiCNAPP Terraform Deployment Options (In this document we will follow Guided Configuration followed by FortiCNAPP CLI Integration):

| **Deployment Method** | **Description** | **Supported Capabilities** |
|------------------------|-----------------|-----------------------------|
| ⚡ **Automated Configuration** | Deploy FortiCNAPP using a **prebuilt Terraform automation flow** that provisions all required resources automatically in a **single AWS region**.<br><br>🔑 *Note:* Requires **temporary credentials** (AWS Access Key ID & Secret Access Key) for your AWS account. | ✅&nbsp;Single&nbsp;AWS&nbsp;Account <br>✅&nbsp;Cloud&nbsp;Config&nbsp;&amp;&nbsp;Audit&nbsp;Logs <br>✅&nbsp;Agentless&nbsp;Workload&nbsp;Scanning |
| <br> | <br> | <br> |
| 🧭 **Terraform via Guided Configuration (UI)** | Deploy and manage FortiCNAPP integrations through the **FortiCNAPP Web Console**.<br><br>Provides a **wizard-based experience** for onboarding and connecting AWS environments without direct CLI interaction. | ✅&nbsp;Single&nbsp;&amp;&nbsp;Multiple&nbsp;AWS&nbsp;Accounts <br>✅&nbsp;Cloud&nbsp;Config&nbsp;&amp;&nbsp;Audit&nbsp;Logs <br>✅&nbsp;EKS&nbsp;Audit&nbsp;Logs |
| <br> | <br> | <br> |
| ⚙️ **Terraform via FortiCNAPP CLI** | Command-line–driven automation using the **open-source FortiCNAPP CLI** (written in Go).<br><br>Ideal for **organization-wide**, **multi-account**, or **DevOps-integrated** deployments. | ✅&nbsp;AWS&nbsp;Organization-level&nbsp;Access <br>✅&nbsp;Single&nbsp;&amp;&nbsp;Multiple&nbsp;AWS&nbsp;Accounts <br>✅&nbsp;Cloud&nbsp;Config&nbsp;&amp;&nbsp;Audit&nbsp;Logs <br>✅&nbsp;EKS&nbsp;Audit&nbsp;Logs <br>✅&nbsp;Agentless&nbsp;Workload&nbsp;Scanning |

---
---  
> 💡 **Note:**  
> You can start with **Guided Configuration** for the Cloud API setup and, if needed, continue using the **FortiCNAPP CLI** — all required tools will be already installed through the Guided Configuration process.  
> Under the hood, the **Guided Configuration** workflow automatically leverages the **FortiCNAPP CLI** to perform deployment actions. 

## 🚀  How  Cloud API (CSPM, CIEM & UEBA) AWS Integration Is Deployed ?  

#### Option.1.Using direct FortiCNAP CLI code (non-interactive). Can be used for CloudShell.
##### Example for Single AWS Account

```bash
lacework generate cloud-account aws --output='/tmp/forti' --config='true' --cloudtrail='true' --aws_region='me-south-1' --noninteractive --apply 
```

---
---  
#### Option.2.Using FortiCNAP CLI (Interactive) Can be used for CloudShell.
##### Example for Single AWS Account
   
#### Note: In CloudShell there’s no local AWS profile and the FortiCNAPP CLI Interactive will ask for innput.
So you can create default AWS profile that points to CloudShell’s region deployment (no need for access keys as CloudShell automatically inherits your current IAM identity).
Let’s create one properly inside your home directory in CloudShell:

```bash
mkdir -p ~/.aws
cat > ~/.aws/config <<'EOF'
[default]
region = me-south-1
credential_source = Ec2InstanceMetadata
EOF
```

| Step | Description |
|------|-------------|
| **Overview** | In this setup, **Terraform** is used via the **FortiCNAPP CLI** to deploy a **single/multi-regional, single-account** or **multi-account** environment. |
| ⚠️ **Pre-Deployment Note** | Make sure you have both:<br>🟦 **AWS Profile** — for your **AWS account integration** or AWS CloudShell<br>🟩 **FortiCNAPP (Lacework) Profile** — for your **FortiCNAPP tenant integration**<br><br>📘 For setup instructions and configuration details, see **[Main AWS Folder `README.md`](../README.md)**. |
| **1-2** | 1. Generate the AWS cloud-account integration using the **FortiCNAPP (Lacework) CLI**:<br>` lacework generate cloud-account aws`<br><br>* 2.(Optional)* Specify a profile if you are using multiple:<br>`lacework generate cloud-account aws --profile default` |



<img width="757" height="336" alt="Screenshot 2025-10-20 at 4 05 46 PM" src="https://github.com/user-attachments/assets/ac673228-bd24-4362-b374-e2c0fafd5f96" />  


<img width="973" height="133" alt="Screenshot 2025-10-20 at 4 10 19 PM" src="https://github.com/user-attachments/assets/89992f5e-628d-41b1-9ec4-bdcc79bd1abe" />






