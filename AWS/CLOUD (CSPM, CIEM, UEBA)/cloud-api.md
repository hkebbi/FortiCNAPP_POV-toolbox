# ☁️ AWS: Cloud API Integration

## 🧠 Why Cloud API Integration ?

Integrates with your AWS Cloud API environment to have the visibiltiy for Configuration Compliance, Cloud Identity risk managemen & and Threat Detection across your cloud environment.
FortiCNAPP connects to AWS through a secure cross-account role to collect **configuration**, **identity**, and **activity** data.  
The following integrations — **CSPM**, **CloudTrail**, and **CIEM** — work together to deliver unified visibility and risk analysis across your AWS environment.

### 🧩 Cloud Security Posture Management (CSPM) — Configuration Scanning

| **Aspect** | **Description** |
|-------------|-----------------|
| **Purpose** | Provides visibility into cloud misconfigurations, security risks, and compliance posture across AWS resources. |
| **Data Source** | AWS Config, EC2, VPC, IAM, S3, and other resource APIs (read via the Lacework/FortiCNAPP cross-account IAM role). |
| **Workflow** | FortiCNAPP uses the IAM role to **read resource configurations** and analyze posture against security best practices and compliance frameworks (CIS, NIST, PCI-DSS, ISO). |
| **Findings** | Detects issues like public S3 buckets, open security groups, disabled encryption, unused keys, and noncompliant configurations. |
| **Outcome** | Continuous configuration visibility and automated compliance posture scoring per AWS account and region. |

---

### 📜 CloudTrail Integration — Activity & Event Monitoring

| **Aspect** | **Description** |
|-------------|-----------------|
| **Purpose** | Collects and analyzes AWS account activity for anomaly detection, behavioral analysis, and forensic visibility. |
| **Data Source** | AWS CloudTrail |
| **Workflow** | CloudTrail delivers logs to S3 → triggers SNS → sends messages to SQS → FortiCNAPP polls SQS to read new log file details → then fetches CloudTrail data from S3 for analysis. |
| **Findings** | Detects suspicious API calls, unauthorized changes, and unusual activity patterns in AWS accounts. |
| **Outcome** | Provides a real-time feed of AWS API activity correlated with configuration and identity data for deep event-based analysis. |

---

### 🔐 Cloud Infrastructure Entitlement Management (CIEM) — AWS Identity Analysis

| **Aspect** | **Description** |
|-------------|-----------------|
| **Purpose** | Analyzes AWS IAM users, roles, and policies to identify over-permissions, risky access, and privilege escalation paths. |
| **Data Source** | AWS IAM, AWS Organizations, AWS Config, and CloudTrail (for identity activity correlation). |
| **Workflow** | FortiCNAPP uses the IAM role to read IAM configurations and correlates that with CloudTrail data to build an **identity graph** showing relationships between users, roles, and permissions. |
| **Findings** | Detects unused roles, overly permissive policies (`*:*`), cross-account trust, orphaned users, and privilege escalation opportunities. |
| **Outcome** | Provides complete IAM visibility, access path analysis, and least-privilege recommendations to strengthen identity security posture. |

---





---
--- 

### 🧱 FortiCNAPP Terraform Deployment Options (In this document we will follow FortiCNAPP CLI Integration):

| **Deployment Method** | **Description** | **Supported Capabilities** |
|------------------------|-----------------|-----------------------------|
| ⚡ **Automated Configuration** | Deploy FortiCNAPP using a **prebuilt Terraform automation flow** that provisions all required resources automatically in a **single AWS region**.<br><br>🔑 *Note:* Requires **temporary credentials** (AWS Access Key ID & Secret Access Key) for your AWS account. | ✅&nbsp;Single&nbsp;AWS&nbsp;Account <br>✅&nbsp;Cloud&nbsp;Config&nbsp;&amp;&nbsp;Audit&nbsp;Logs <br>✅&nbsp;Agentless&nbsp;Workload&nbsp;Scanning |
| <br> | <br> | <br> |
| 🧭 **Terraform via Guided Configuration (UI)** | Deploy and manage FortiCNAPP integrations through the **FortiCNAPP Web Console**.<br><br>Provides a **wizard-based experience** for onboarding and connecting AWS environments without direct CLI interaction. | ✅&nbsp;Single&nbsp;&amp;&nbsp;Multiple&nbsp;AWS&nbsp;Accounts <br>✅&nbsp;Cloud&nbsp;Config&nbsp;&amp;&nbsp;Audit&nbsp;Logs <br>✅&nbsp;EKS&nbsp;Audit&nbsp;Logs |
| <br> | <br> | <br> |
| ⚙️ **Terraform via FortiCNAPP CLI** | Command-line–driven automation using the **open-source FortiCNAPP CLI** (written in Go).<br><br>Ideal for **organization-wide**, **multi-account**, or **DevOps-integrated** deployments. | ✅&nbsp;AWS&nbsp;Organization-level&nbsp;Access <br>✅&nbsp;Single&nbsp;&amp;&nbsp;Multiple&nbsp;AWS&nbsp;Accounts <br>✅&nbsp;Cloud&nbsp;Config&nbsp;&amp;&nbsp;Audit&nbsp;Logs <br>✅&nbsp;EKS&nbsp;Audit&nbsp;Logs <br>✅&nbsp;Agentless&nbsp;Workload&nbsp;Scanning |

---
---  




### ☁️ AWS & FortiCNAPP-CLI Terraform Prerequisites

| **Component / Requirement** | **Description** | **Reference / Link** |
|------------------------------|-----------------|----------------------|
| 🧑‍💻 **AWS Account Admin** | Administrative privileges on each AWS account.<br>Required during onboarding process only. | — |
| 🔧 **AWS CLI** | Install and configure the AWS CLI to connect to your AWS Account. | [AWS CLI Installation Guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) |
| 🧰 **Linux Tools** | Ensure basic tools (`curl`, `git`, `unzip`) are available in the system `PATH`. | — |
| ⚙️ **Terraform** | Install Terraform if not already configured. | [Terraform Documentation](https://developer.hashicorp.com/terraform) |
| 🧠 **FortiCNAPP CLI** | Open-source CLI tool written in Golang. Available for **Linux**, **macOS**, and **Windows**.<br>Used to interact with FortiCNAPP via command line. | [FortiCNAPP CLI Guide](https://docs.fortinet.com/document/forticnapp/latest/cli-reference/68020/get-started-with-the-lacework-forticnapp-cli) |
| ⚡ **Deployment Methods** | Supported installation environments and automation options:<br>• **AWS Cloud Shell**<br>• **Hosts Supported by Terraform** | — |

---
---  


# 🚀 How Agentless Cloud API Integration Is Deployed ?  



---
---  
 
### 🛡️ Verify from UI (After 24 hours) Compliance, Identiies, Cloud Logs & Resource Inventory Tabs :  




---
---  

## 🧩 What Is Deployed in a Workflow + Definitions ?  




## 🧠 FortiCNAPP AWS Integrations Overview



---








---
---  


---
---  
 

## 🔗 Reference Links




# 🚀 How AWS Cloud API Integration (CSPM, CIEM, UEBA) Is Deployed ?

## 🧩 What the “PATH” Really Is

| **Section** | **Description** |
|--------------|-----------------|
| **Overview** | When you open your **CloudShell** (or any Linux/macOS terminal), your shell — like **bash** or **zsh** — doesn’t automatically know where every program is located. Instead, it searches for programs only inside specific folders. These folders are listed in an environment variable called **`$PATH`**. |
| **What `$PATH` Does** | Think of **`$PATH`** as a *list of folders* your shell searches **in order** whenever you type a command. |
| **Goal** | Make CloudShell search inside your **`forticnapp`** folder so it can find and run the **FortiCNAPP CLI** from there. |
| **Commands to Run** | **Command (This example with Forticnapp folder created already** | **Purpose** |
|  | `echo 'export PATH="$HOME/forticnapp:$PATH"' >> ~/.bashrc` | Adds your `forticnapp` folder to the PATH permanently. |
|  | `source ~/.bashrc` | Reloads your updated PATH immediately. |
| **Verification** | **Command** | **Expected Output** |
|  | `echo $PATH` | Should show `/home/cloudshell-user/forticnapp` at the **front** of the PATH. |
| **💡 Explanation** | **Part** | **What It Does** |
|  | `echo` | Prints (outputs) text. |
|  | `'export PATH="$HOME/forticnapp:$PATH"'` | Updates PATH to include `/home/cloudshell-user/forticnapp` **before** other paths. |
|  | `>> ~/.bashrc` | Appends that line to your **.bashrc** file — which runs every time a new terminal starts. |
|  | `source ~/.bashrc` | Reloads **.bashrc** immediately, applying your changes. |
| **Plain English Summary** | “Whenever I open my shell, make sure my `forticnapp` directory is included in my PATH so I can run any programs I put there.” |


## 🏠 Install on Your Home Directory

| **Step** | **Command / Output** | **Description** |
|-----------|----------------------|-----------------|
| 1 | `wget https://raw.githubusercontent.com/lacework/go-sdk/main/cli/install.sh \| bash` | Download and run the Lacework (FortiCNAPP) CLI installer script. |
| 2 | `chmod +x install.sh` | Make the installer script executable. |
| 3 | `sudo ./install.sh` | Run the installer — installs the Lacework CLI into your home directory. *(If CloudShell blocks `/usr/local/bin`, install manually under `$HOME/forticnapp`.)* |
| 4 | `lacework version` | Verify the installation — expected output example: <br> `lacework v2.8.1 (sha:ef54b4ad33d3bd73f9892d48439bb52c499ec1dc) (time:20251006212503)` |


## ⚙️ Configure Lacework (FortiCNAPP) CLI (API Key from FortiCNAPP UI Settings)

| **Step** | **Command / Output** | **Description** |
|-----------|----------------------|-----------------|
| 1 | `lacework configure` | Start the interactive setup process for the FortiCNAPP (Lacework) CLI. |
|   | **CLI Prompts:** |  |
|   | ▸ Account: `3555505.lacework.net` | Enter your tenant account. You can omit `.lacework.net`; the CLI will automatically trim it. |
|   | ▸ Account Confirmation: `Using '3555505'` | Confirms account format. |
|   | ▸ Access Key ID: `xxxxF1_4FD805727BC2F0951D58Eyyy` | Paste your FortiCNAPP API key ID. |
|   | ▸ Secret Access Key: `*********************************` | Paste your API secret (hidden for security). |
| 2 | `lacework version` | Verify CLI installation and configuration — expected output example: <br>`lacework v2.8.1 (sha:ef54b4ad33d3bd73f9892d48439bb52c499ec1dc) (time:20251006212503)` |  

Note: In CloudShell there’s no local AWS profile, so Terraform will fail .
This creates a default AWS profile that points to CloudShell’s temporary IAM role credentials (no need for access keys).
Let’s create one properly inside your home directory in CloudShell:
mkdir -p ~/.aws

cat > ~/.aws/config <<'EOF'
[default]
region = me-south-1
credential_source = Ec2InstanceMetadata
EOF




## 🔧 AWS Cloud Account Configuration Workflow (FortiCNAPP) Using FortiCNAPP CLI


| Step | Description |
|------|-------------|
| **Overview** | In this setup, **Terraform** is used via the **FortiCNAPP CLI** to deploy a **single/multi-regional, single-account** or **multi-account** environment. |
| ⚠️ **Pre-Deployment Note** | Make sure you have both:<br>🟦 **AWS Profile** — for your **AWS account integration** or AWS CloudShell<br>🟩 **FortiCNAPP (Lacework) Profile** — for your **FortiCNAPP tenant integration**<br><br>📘 For setup instructions and configuration details, see **[Main AWS Folder `README.md`](../README.md)**. |
| **1-2** | 1. Generate the AWS cloud-account integration using the **FortiCNAPP (Lacework) CLI**:<br>` lacework generate cloud-account aws`<br><br>* 2.(Optional)* Specify a profile if you are using multiple:<br>`lacework generate cloud-account aws --profile default` |



<img width="757" height="336" alt="Screenshot 2025-10-20 at 4 05 46 PM" src="https://github.com/user-attachments/assets/ac673228-bd24-4362-b374-e2c0fafd5f96" />  


<img width="973" height="133" alt="Screenshot 2025-10-20 at 4 10 19 PM" src="https://github.com/user-attachments/assets/89992f5e-628d-41b1-9ec4-bdcc79bd1abe" />



| **Component** | **Description** | **FortiCNAPP Usage / Notes** |
|----------------|-----------------|-------------------------------|
| 📨 **SNS Topic** | An **Amazon Simple Notification Service (SNS)** topic is a logical access point that acts as a communication channel, grouping multiple endpoints such as AWS, Amazon SQS, HTTP/S, or email addresses. | Required for **all CloudTrail integrations**.<br>FortiCNAPP can use an **existing SNS topic** or create one automatically if not present. |
| 📬 **SQS Queue** | **Amazon Simple Queue Service (SQS)** enables distributed applications to exchange messages asynchronously. SNS and SQS together deliver notifications immediately while persisting messages for later processing. | Required for **all CloudTrail integrations**.<br>Used alongside SNS to deliver CloudTrail events to FortiCNAPP. |
| 🗃️ **S3 Bucket** | An **Amazon S3 bucket** is a container for objects. CloudTrail stores log files in S3 buckets. | Required for **all CloudTrail integrations**.<br>FortiCNAPP can use an existing S3 bucket or create one in the designated AWS account. |
| ☁️ **CloudTrail** | **AWS CloudTrail** records user, role, or service actions across your AWS account for governance, compliance, and audit purposes. | Required to capture and deliver event logs to FortiCNAPP.<br>FortiCNAPP can use an **existing CloudTrail** or create a new one. |
| 🔐 **IAM Cross-Account Role** | An **IAM cross-account role** grants FortiCNAPP access to AWS resources for configuration assessment and CloudTrail event analysis. | Required for **read-only access**.<br>Includes two policies:<br>• **FortiCNAPP custom audit policy** – read-only access to configuration resources.<br>• **FortiCNAPP custom IAM policy** – read-only access to ingest CloudTrail logs. |
| ⚠️ **Important Note** | Ensure your **IAM cross-account role** and **S3 bucket** are created in the **same AWS account** — regardless of setup method (manual, CloudFormation, or Terraform). | This is due to **legacy AWS access control rules**. Cross-account access alone is **not sufficient** to bypass these S3 restrictions. |
