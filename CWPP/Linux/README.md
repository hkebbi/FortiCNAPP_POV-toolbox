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

```bash
Deployment using Lacework PowerShell Script Flow
├ 1. Create New Agent Access Token. 
├ 2. Install Using "Lacework Script" via FortiCNAPP Console.
├ 3.1. Deployment using Lacework Script.
├ 3.2. Deployment using Lacework Script (with Proxy Settings)

```
#### ✅ 1. Create New Agent Access Token:

- Lacework FortiCNAPP Console, go to Settings > Configuration > Agent Tokens.
- Add Name and Description.
- Select Linux as the Operating System.
- Click Save to create your new access token for Linux agent.
  
* Note: An access token can be re-used for multiple agent installations.

#### ✅ 2. Install Using Lacework Script via FortiCNAPP Console:
 In the Lacework FortiCNAPP Console, go to Settings > Configuration > Agent Tokens.
- Select = the Linux access token created and that you want to use for your agent installation.
- Click the Install tab.
- Click  installation method, "Lacework Script":
- Copy URL
  
#### ✅ 3.1. Deployment using Lacework Script:

 -  Open Linux terminal.
 -  wget and Paste URL(from step 2) on the Linux Machine.
 -  sudo sh install.sh

```bash
wget https://x.lacework.net/mgr/v1/download/1162e0f6cf22890b4242c00ce2a725c11341136575d77e23c1311566/install.sh
sudo sh install.sh
```
Run the following command to verify the agent process (datacollector) status:
```bash
sudo /var/lib/lacework/datacollector -status
```

#### ✅ 3.2. Deployment using Lacework Script (With Proxy Settings_:

 -  Open Linux terminal.
 -  wget and Paste URL(from step 2) on the Linux Machine.
 -  sudo sh install.sh

 -  




#### ✅ 3.2. Deployment using Lacework PowerShell Script (with Proxy settings) :

To configure the agent to use a specific proxy during installation in the command line, use the following command:C:\Users\Administrator>  
```bash
msiexec.exe /i LWDataCollector.msi ACCESSTOKEN=Your_Access_Token SERVERURL=Your_API_Endpoint PROXYURL=http://Your_Proxy_Server:Your_Port
```
- Where Your_Proxy_Server is the URL or IP address of your HTTP proxy server and Your_Port is the port number of your proxy server.

If the agent should not use a proxy, regardless of the machine’s configuration, use the following command during installation:

#### ✅ 4. Verify, Restart, Troubleshoot: C:\Users\Administrator>

systemctl [start | stop | restart] datacollector
service datacollector [start | stop | restart]
initctl [start | stop | restart] datacollector

Run the following command to verify the agent process (datacollector) status:
sudo /var/lib/lacework/datacollector -status

### ✅ Reference Links


Linux agent overview and system requirements
https://docs.fortinet.com/document/forticnapp/latest/administration-guide/698784/linux-agent-overview-and-system-requirements



