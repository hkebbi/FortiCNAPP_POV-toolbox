# Deploying FortiCNAPP Linux Agent using Lacework PowerShell Script (POV)

The Lacework FortiCNAPP Linux agent provides threat detection, file integrity monitoring, vulnerability detection, and host-based intrusion detection for your cloud or on-premise Linux OS-based workloads.
After you install the agent, the agent and Lacework FortiCNAPP server communicate with each other. The agent scans your host and securely forwards select metadata to the server to build a baseline of normal behavior. From this, Lacework FortiCNAPP provides alerts for anomalous behavior.


### ⚙️ Agent Consumptions

| **Metric** | **Average / Approximate Usage** | **Notes** |
|-------------|---------------------------------|------------|
| 🧠 **CPU Usage** | **1–3% (average)** | Lightweight background process; typically below 2% during idle states. |
| 💾 **Memory Usage** | **250–300 MB (average)** | Slight variations depending on workload and monitoring activity. |
| 📂 **Disk Space** | **~250 MB** | Approximate space required for agent binaries and logs. |
| 🌐 **Data Usage** | **1–2 KB/sec (average)** | Minimal network footprint for telemetry and status updates. |

---

---
#### ✅ Deployment using Lacework PowerShell Script Flow:
Fits All Clouds: Install the Windows Agent on Hosts Using Lacework PowerShell Script, Quick Easy installion method on few machines during Lab/POC.
There are many other methods as above, but this can fit a quick easy way for this kind of deployments if there are few machines and multicloud deployments.

```bash
Deployment using Lacework PowerShell Script Flow
├ 1. Create New Access Token. 
├ 2. Download/Install Using Lacework PowerShell Script via FortiCNAPP Console.
├ 3. Deployment using Lacework PowerShell Script  without a Configuration File.
├ 4. Verify.
```
#### ✅ 1. Create New Access Token:

- Lacework FortiCNAPP Console, go to Settings > Configuration > Agent Tokens.
- Add Name and Description.
- Select Windows as the Operating System.
- Click Save to create your new access token for Windows agent.
  
* Note: An access token can be re-used for multiple agent installations.

#### ✅ 2. Download/Install Using Lacework PowerShell Script via FortiCNAPP Console:
 In the Lacework FortiCNAPP Console, go to Settings > Configuration > Agent Tokens.
- Select = the Windows access token created and that you want to use for your agent installation.
- Click the Install tab.
- Click  installation method, Lacework PowerShell Script:
- Do either of the following:
  - Click Download script to download the file (powershell.zip file).
  - Unzip "powershell.zip" and you get 3 files (Azure-Deploy-LW-Win.ps1, Install-LWDataCollector.ps1 & lwminisign.exe).
<img width="434" height="103" alt="Screenshot 2025-07-30 at 1 43 34 PM" src="https://github.com/user-attachments/assets/01b0b44c-b2e1-4a39-84ef-240a22070ea4" />

#### ✅ 3. Deployment using Lacework PowerShell Script:
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

#### ✅ 3. Deployment using Lacework PowerShell Script (with Proxy settings) :

To configure the agent to use a specific proxy during installation in the command line, use the following command:C:\Users\Administrator>  
```bash
msiexec.exe /i LWDataCollector.msi ACCESSTOKEN=Your_Access_Token SERVERURL=Your_API_Endpoint PROXYURL=http://Your_Proxy_Server:Your_Port
```
- Where Your_Proxy_Server is the URL or IP address of your HTTP proxy server and Your_Port is the port number of your proxy server.

If the agent should not use a proxy, regardless of the machine’s configuration, use the following command during installation:

#### ✅ 4. Verify, Restart, Troubleshoot: C:\Users\Administrator>

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

Stop the window agent with the following command:  

```bash
sc.exe stop lwdatacollector
```

Restart the window agent with the following command:  

```bash
sc.exe start lwdatacollector
```

Unistall the window agent with the following powershell command:  
 - Do not downgrade to an earlier version of the agent. Downgrading can cause data incompatibility.

```bash
## Script to uninstall the Lacework Windows agent
$app = Get-WmiObject -Class Win32_Product -Filter "Name like '%Lacework%'"
$app.Uninstall()
```

### ✅ Reference Links


Linux agent overview and system requirements
https://docs.fortinet.com/document/forticnapp/latest/administration-guide/698784/linux-agent-overview-and-system-requirements



