# ☁️ Azure: Cloud API Integration



Requirements
Azure Global Administrator - An Azure portal account that has a Global Administrator role for your tenant's directory.
Azure Owner Role - An Azure portal account with the Owner role in all subscriptions that you want to monitor.
Lacework Administrator - A Lacework account with administrator privileges.`
Next Steps
As an account with global administrator access and owner privileges to the subscription being used, go to the Azure Cloud Shell.
Paste the command and press enter. This downloads the Lacework CLI, sets up the CLI with your configuration, calls the CLI non-interactively, and applies Terraform. When the command finishes, the new integration appears in the Cloud accounts list after you refresh the Lacework Console screen.


## 🧠 Why Cloud API Integration ?

Integrates with your Azure Cloud API environment to have the visibiltiy for Configuration Compliance, Cloud Identity risk managemen & and Threat Detection across your cloud environment.
FortiCNAPP connects to Azure through a secure cross-account role to collect **configuration**, **identity**, and **activity** data.  
The following integrations — **CSPM**, **Activity Log**, and **CIEM** — work together to deliver unified visibility and risk analysis across your Azure environment.



## ☁️ Azure Cloud Security Posture Management (CSPM) — Configuration Workflow

| Aspect | Description |
|--------|--------------|
| **Purpose** | Provides comprehensive visibility into Azure configuration risks, misconfigurations, and compliance posture across subscriptions, tenants, and resources. |
| **Data Source** | **Reader Role (Subscription Level)** – Grants read-only access to view Azure resources and their configurations within a subscription, without the ability to modify them. This allows FortiCNAPP to collect posture and compliance data across supported Azure services such as compute, networking, storage, and security configurations.<br>**Key Vault Reader Role** – Allows FortiCNAPP to access metadata of all Key Vaults in the subscription. Any new Key Vaults added will automatically be included for compliance monitoring.<br>**Directory Reader Role (Microsoft Entra ID)** – Enables FortiCNAPP to collect directory objects (users, groups, members, and app registrations) from Microsoft Entra ID using Microsoft Graph API calls. This identity data supports LQL (Lacework Query Language) datasources and IAM compliance policies. |
| **Workflow** | FortiCNAPP connects using the Azure service principal with the above roles. It reads configuration and identity data from Azure services and evaluates them against built-in security and compliance frameworks (CIS, NIST, PCI-DSS, ISO, etc.). When Directory Reader is granted, identity-based posture and IAM compliance checks are also performed. |
| **Findings** | Identifies misconfigurations such as open Network Security Groups (NSGs), publicly accessible storage or Key Vaults, disabled encryption, missing diagnostic settings, and non-compliant access policies. |
| **Outcome** | Provides continuous configuration visibility, compliance scoring, and least-privilege posture assessment across all Azure subscriptions and directories. |
| **Notes** | If the **Directory Reader** role is **disabled**, FortiCNAPP will not collect Entra ID (Azure AD) user, group, or app registration data. As a result, related **LQL datasources** and **IAM compliance policies** will not be evaluated—this may be required in environments with strict privacy or regulatory controls. |

-----
-----

## 📜 Azure Activity Log Integration — Configuration Workflow

### 🔁  Azure Activity - Terms Definition notes (General)

| Stage | What It Does | Data Type |
|--------|---------------|-----------|
| **Azure Monitor Diagnostic Settings** | Exports Azure Activity Logs from each subscription to a centralized Storage Account under `insights-activity-logs/` | Activity Log JSON files |
| **Event Grid** | Detects creation of new Activity Log blobs in the Storage Account and triggers notifications when new data is available | Event notification |
| **Storage Queue** | Receives and stores notifications from Event Grid containing the blob path and metadata about newly created log files | JSON message |

-----
### 📜 Azure Activity - Configuration Workflow
| Aspect | Description |
|--------|--------------|
| **Purpose** | Collects and analyzes Azure subscription activity for anomaly detection, behavioral analysis, and forensic visibility. |
| **Data Source** | **Azure Activity Logs** – Captures control-plane operations.<br>**Custom Role** – Allows FortiCNAPP to read storage, queues, Event Grid, and keys via Azure APIs. |
| **Workflow** | Azure Monitor exports Activity Logs → logs written hourly to a central Storage Account (`insights-activity-logs`) → Event Grid detects new blobs → sends notifications to a Storage Queue → Lacework FortiCNAPP leverages an Event Grid subscription that creates Queue messages → Lacework FortiCNAPP pulls the new logs as soon as they appear. |
| **Findings** | Detects suspicious admin actions, unauthorized changes, and unusual control-plane activity. |
| **Outcome** | Provides near real-time visibility into Azure operations with correlated identity and configuration insights. |




If choosing to grant permissions to the directory through the Directory Reader role, Lacework FortiCNAPP will collect the list of users, groups, members, and app registrations from the Entra ID organization using Microsoft Graph API calls. This information is exposed for LQL datasources and compliance policies.


lacework generate --output "/home/hussam/azure-cloud-api" cloud-account azure --configuration='true' --activity_log='true' --location=‘West Europe' --ad_create='true' --subscription_id='03921cd3-caef-45a2-bc47-98e889f270a0’  --noninteractive


<img width="578" height="148" alt="Screenshot 2025-11-10 at 1 07 40 PM" src="https://github.com/user-attachments/assets/aec0b263-ff63-4c31-ac94-344d1614f358" />

<img width="1267" height="395" alt="Guided-config" src="https://github.com/user-attachments/assets/72d88d48-fc60-4b97-ae4e-6b64c48c1948" />

<img width="661" height="466" alt="Screenshot 2025-11-10 at 12 59 23 PM" src="https://github.com/user-attachments/assets/eb53de80-e1b8-46c4-bde8-8a39db6440ac" />


--> Generate CLI Bundle

<img width="679" height="556" alt="Screenshot 2025-11-10 at 1 00 43 PM" src="https://github.com/user-attachments/assets/74e76cda-f946-4760-b239-639c5589d063" />


<img width="1329" height="54" alt="Screenshot 2025-11-10 at 1 01 05 PM" src="https://github.com/user-attachments/assets/4d2199eb-384e-40ae-847a-23203e98b54f" />

<img width="1165" height="132" alt="Screenshot 2025-11-10 at 1 06 07 PM" src="https://github.com/user-attachments/assets/2864c5d1-6d51-4522-9b3d-a3ce0dd2cd50" />


<img width="740" height="391" alt="Screenshot 2025-11-10 at 1 06 59 PM" src="https://github.com/user-attachments/assets/e00561bc-520a-42b9-b701-4265a795cfdc" />


<img width="1287" height="493" alt="Screenshot 2025-11-10 at 1 09 17 PM" src="https://github.com/user-attachments/assets/c99c8aa5-43f8-403f-9551-32695b070947" />

<img width="1286" height="323" alt="Screenshot 2025-11-10 at 1 10 33 PM" src="https://github.com/user-attachments/assets/73eb2db3-0ea9-41c5-86a3-2ff59cbc5d46" />
