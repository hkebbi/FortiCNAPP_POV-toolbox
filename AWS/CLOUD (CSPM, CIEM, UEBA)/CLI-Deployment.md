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


## 🔧 AWS Cloud Account Configuration Workflow (FortiCNAPP) Using FortiCNAPP CLI


| Step | Description |
|------|-------------|
| **Overview** | In this setup, **Terraform** is used via the **FortiCNAPP CLI** to deploy a **single/multi-regional, single-account** or **multi-account** environment. |
| ⚠️ **Pre-Deployment Note** | Make sure you have both:<br>🟦 **AWS Profile** — for your **AWS account integration** or AWS CloudShell<br>🟩 **FortiCNAPP (Lacework) Profile** — for your **FortiCNAPP tenant integration**<br><br>📘 For setup instructions and configuration details, see **[Main AWS Folder `README.md`](../README.md)**. |
| **1-2** | 1. Generate the AWS cloud-account integration using the **FortiCNAPP (Lacework) CLI**:<br>` lacework generate cloud-account aws`<br><br>* 2.(Optional)* Specify a profile if you are using multiple:<br>`lacework generate cloud-account aws --profile default` |



<img width="757" height="336" alt="Screenshot 2025-10-20 at 4 05 46 PM" src="https://github.com/user-attachments/assets/ac673228-bd24-4362-b374-e2c0fafd5f96" />  


<img width="973" height="133" alt="Screenshot 2025-10-20 at 4 10 19 PM" src="https://github.com/user-attachments/assets/89992f5e-628d-41b1-9ec4-bdcc79bd1abe" />
