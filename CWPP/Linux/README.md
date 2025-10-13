# Deploying FortiCNAPP Linux Agent using Lacework PowerShell Script (POV) on hosts:

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
Deployment using Lacework PowerShell Script file Flow:
├ 1. Create New Agent Access Token. 
├ 2. Install Using "Lacework Script" via FortiCNAPP Console.
├ 3.1. Deployment using Lacework Script.
├ 3.2. Deployment using Lacework Script (with Proxy Settings)
├ 4. Verify, Restart, Troubleshoot
❓FAQ
🐧Linux Agent Reference Links
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

#### ✅ 3.2. Deployment using Lacework Script (With Proxy Settings):
Specify the HTTP or SOCKS proxy server for the Lacework FortiCNAPP agent to use as a network proxy 

| **Scenario**             | **Example**                                     | **Description**                            |
| ------------------------ | ----------------------------------------------- | ------------------------------------------ |
| Standard proxy (no auth) | `http://proxy.company.com:8080`                 | Uses a basic proxy without authentication. |
| Proxy with credentials   | `http://user123:pass456@proxy.company.com:8080` | Connects using authentication credentials. |
| SOCKS proxy              | `socks5://proxy.company.com:1080`               | Example using a SOCKS5 proxy protocol.     |

💡 Tip:
Before applying your configuration in production, verify the proxy connection and credentials.
You can test connectivity with commands like:

```bash
curl -v --proxy http://Your_Proxy_Server:Your_Port https://example.com
```

You can add this on the FortiCNAPP UI Agent Configure Settings.

#### ✅ 4.1. Verify, Start, Stop, Restart, Troubleshoot: C:\Users\Administrator>

The Lacework FortiCNAPP Linux agent service is named datacollector. Once installed, you can use Linux utilities like service, initctl, or systemctl can to manage the service. Common commands are:  

Run the following command to verify the agent process (datacollector) status:
```bash
sudo /var/lib/lacework/datacollector -status
```
```bash
systemctl [start | stop | restart] datacollector
```
```bash
service datacollector [start | stop | restart]
```
```bash
initctl [start | stop | restart] datacollector
```

You can use the following package-specific commands to remove all files, including the configuration and log files created by the agent.  
```bash
apt purge lacework
dpkg --purge lacework
rpm -e lacework
yum remove lacework
```

## ❓ Frequently Asked Questions (FAQ)

| **Question** | **Answer (Summary)** |
|---------------|----------------------|
| 🧩 **Does the Linux agent install kernel modules?** | **No.** The Linux agent runs entirely in **user mode** using **safe eBPF programs**, avoiding risks associated with kernel modules. |
| 🔐 **Are root privileges required for installation?** | **Yes.** Installation requires **root privileges** — either log in as root or run the installer with `sudo`. |
| 📦 **Does the agent have any package dependencies?** | **No.** The agent installs with **no external package dependencies** and does **not** install shared libraries. |
| ⚙️ **Does the agent work in kernel or user space?** | The agent operates in **user space** and in **passive mode**. It has **no dependency on IP tables** and does **not impact container or network performance**. |
| ⏱️ **How often does the agent collect data?** | The agent continuously monitors metadata from active processes. The **Polygraph** (behavioral model) is computed **every hour**. |
| 💾 **What happens if the agent cannot connect to Lacework FortiCNAPP?** | The agent buffers up to **40 MB of compressed data** (~4 hours). If exceeded, it drops the **oldest data (FIFO)**. |
| 🚀 **How can I deploy the agent?** | You can deploy the agent using **Chef, Puppet, Ansible, Salt**, or the official **Fortinet installation script (This file)**. |
| 🌐 **Does the agent support a proxy configuration?** | **Yes.** Proxy support is available by adding proxy info to the config file or setting the **`https_proxy`** environment variable. |
| 🔒 **Is data encrypted in transit?** | **Yes.** All data is encrypted **in transit** via **HTTPS (port 443)** using **TLS 1.2**. |
| 🗜️ **Is data compressed before transmission?** | **Yes.** Data is **compressed end-to-end** before being sent to the Lacework FortiCNAPP platform. |
| 🏷️ **Does the agent support custom tags?** | **Yes.** The agent imports **AWS, Google Cloud, and Azure tags**, and also supports adding **local custom tags**. |

---

> 💡 **Tip:**  
> The Lacework FortiCNAPP Agent is designed for **safe, efficient, and low-overhead monitoring** across both **on-premises** and **cloud environments**. It ensures security without degrading host or container performance.




### 🐧 Linux Agent Reference Links

| **Topic** | **Description** | **Link** |
|------------|-----------------|----------|
| 🧠 **Linux Agent Overview & System Requirements** | Detailed overview of supported OS versions, system requirements, and functionality. | [View Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/698784/linux-agent-overview-and-system-requirements) |
| 🧹 **Uninstalling the Linux Agent** | Step-by-step instructions for safely removing the Linux agent from hosts. | [View Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/981809/uninstall-the-linux-agent) |
| ✅ **Linux Agent Install Checklist** | Pre-installation checklist for verifying system readiness and environment compatibility. | [View Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/376786/linux-agent-install-checklist) |
| 🌐 **Required Connectivity & Proxy Settings** | Lists network allowlist, proxy, and certificate requirements for agent connectivity. | [View Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/59862/required-connectivity-proxies-and-certificates-for-agents) |
| 💻 **Installing the Linux Agent on Hosts** | Guides for deploying the Linux agent on host machines. | [View Docs](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/001333/install-on-hosts) |


