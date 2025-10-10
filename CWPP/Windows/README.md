# Deployment using Lacework PowerShell Script (POV)

🛡️ Windows agent overview and system requirements
```bash
https://docs.fortinet.com/document/lacework-forticnapp/latest/administration-guide/662064/windows-agent-overview-and-system-requirements
```

https://docs.fortinet.com/document/forticnapp/latest/administration-guide/1455/downloading-the-windows-agent-installer

### ✅ Side Note: Other Installation Methods for Windows Servers Supported
1. All Clouds: Install the Windows Agent on Hosts Using Lacework PowerShell Script (Quick Easy Manual install on few machines), Guide on This file.
2. All Clouds: MSI Package as long as Windows VM is domain-joined or managed (Mass deployment via GPO/SCCM/Intune).
3. Azure Specific (Windows VMs in an Azure resource group): Install the Windows Agent on Azure VMs Using a PowerShell Script.
4. Azure Specific: Install Windows Agent with Azure Resource Manager.
5. Azure Specific: Install Windows Agent on Azure VMs with Terraform.
6. AWS Specific: Install Windows Agent on AWS with Packer
7. EKS & AKS Specific: Using Helm Chart

---
#### ✅ Deployment using Lacework PowerShell Script Flow:
The example here is to deploy during Lab/POC which can have few machines for testing. There are many options, but this can fit a quick easy way for this kind of deployments if there are few machines and even multicloud deployments.

```bash
Deployment using Lacework PowerShell Script Flow
├ 1. Create New Access Token. 
├ 2. Download/Install Using Lacework PowerShell Script via FortiCNAPP Console.
├ 3. Deployment using Lacework PowerShell Script  without a Configuration File.
├ 4. Verify.
```
#### ✅ 1. Create New Access Token

- Lacework FortiCNAPP Console, go to Settings > Configuration > Agent Tokens.
- Add Name and Description.
- Select Windows as the Operating System.
- Click Save to create your new access token for Windows agent.
* An access token can be re-used for multiple agent installations.

#### ✅ 2. Download/Install Using Lacework PowerShell Script via FortiCNAPP Console .
All Clouds: Install the Windows Agent on Hosts Using Lacework PowerShell Script (Quick Easy Manual install on few machines)
 In the Lacework FortiCNAPP Console, go to Settings > Configuration > Agent Tokens.
- Select the row for the Windows access token you want to use for your agent installation.
- Click the Install tab.
- Click the desired installation method (Lacework PowerShell Script)
- Do either of the following:
  - Click Download script to download the file.
  - Click Copy URL to copy the URL to use later (3 files will be downloaded from zip file)
<img width="434" height="103" alt="Screenshot 2025-07-30 at 1 43 34 PM" src="https://github.com/user-attachments/assets/01b0b44c-b2e1-4a39-84ef-240a22070ea4" />

#### ✅ 3. Deployment using Lacework PowerShell Script  without a Configuration File:
Instead of specifying configuration parameters for the agent installation in a config.json file, you can specify them directly in the command line:

 - 1. Open PowerShell terminal as an administrator.
 - 2. Navigate to the directory containing the Install-LWDataCollector.ps1 script on your host.
 - 3. Run the script using the following command in the PowerShell command line, C:\Users\Administrator>:
```bash
.\Install-LWDataCollector.ps1 -MSIURL Agent_MSI_Download_URL -AccessToken Your_Access_Token -ServerURL Your_API_Endpoint -Defender

```
Example for Windows Agent Release 1.7.2:
```bash
.\Install-LWDataCollector.ps1 -MSIURL https://updates.lacework.net/win-1.7.2.3973-2023-11-05-release-1.7.0-cc74651519014fec0f7502858b06895a4cf0d802/LWDataCollector.msi  -AccessToken b2fxxxxxx -ServerURL https://lwxx-eu.lacework.net  -Defender
```

Where:
Your_Access_Token:  Specifies your agent access token. Token Details under Agent Token (example: b2fe1bb5axxxxx). Step.1.
Your_API_Endpoint: Specifies your Lacework FortiCNAPP agent server URL. (example: https://xx-eu.lacework.net )
-Defender option: Excludes the Windows agent from scanning with Windows Defender.
Agent_MSI_Download_URL: Copy the URL for Lacework Windows Agent MSI Package from:
```bash
https://github.com/lacework/lacework-windows-agent-releases/releases
```
A config.json file  that contains the options you specified in the command line is created in the C:\ProgramData\Lacework\ directory. 
You can modify this file to change the settings for the agent. If you modify the file, you must restart the agent for the changes to take effect. For more information, see Restart the Windows Agent.


#### ✅ 4. Verify, Restart, Troubleshoot:
Enter the following command to check status for the lwdatacollector service: 
- In Powersehll:
```bash
sc.exe query lwdatacollector
```
or
- In Command Prompt
```bash
sc query lwdatacollector
```



