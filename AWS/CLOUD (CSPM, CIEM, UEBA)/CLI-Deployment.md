# 🚀 How AWS Cloud API Integration (CSPM, CIEM, UEBA) Is Deployed ?

## 🧩 What the “PATH” Really Is

| **Section** | **Description** |
|--------------|-----------------|
| **Overview** | When you open your **CloudShell** (or any Linux/macOS terminal), your shell — like **bash** or **zsh** — doesn’t magically know where every program is located. Instead, it searches for programs only inside specific folders. These folders are listed in an environment variable called **`$PATH`**. |
| **What `$PATH` Does** | Think of **`$PATH`** as a *list of folders* your shell searches **in order** when you type a command. |
| **Goal** | Make CloudShell search inside your **`forticnapp`** folder so it can find and run the **FortiCNAPP CLI** from there. |
| **Commands to Run** | ```bash<br># Add the forticnapp directory to PATH<br>echo 'export PATH="$HOME/forticnapp:$PATH"' >> ~/.bashrc<br>source ~/.bashrc``` |
| **Verification** | ```bash<br>echo $PATH<br>``` <br>You should see `/home/cloudshell-user/forticnapp` at the **front** of the PATH output. |
| **💡 Explanation** | **Part** | **What It Does** |
|  | `echo` | Prints (outputs) text. |
|  | `'export PATH="$HOME/forticnapp:$PATH"'` | This updates the PATH by adding `/home/cloudshell-user/forticnapp` **in front** of whatever PATH already exists. |
|  | `>> ~/.bashrc` | Appends that line to your **.bashrc** configuration file — this file runs automatically every time you start a new terminal session. |
|  | `source ~/.bashrc` | Reloads **.bashrc** immediately so your changes take effect without restarting. |
| **Plain English Summary** | “Whenever I open my shell, make sure my `forticnapp` directory is included in my PATH so I can run any programs I put there.” |








## 🔧 AWS Cloud Account Configuration Workflow (FortiCNAPP) Using FortiCNAPP CLI


| Step | Description |
|------|-------------|
| **Overview** | In this setup, **Terraform** is used via the **FortiCNAPP CLI** to deploy a **single/multi-regional, single-account** or **multi-account** environment. |
| ⚠️ **Pre-Deployment Note** | Make sure you have both:<br>🟦 **AWS Profile** — for your **AWS account integration** or AWS CloudShell<br>🟩 **FortiCNAPP (Lacework) Profile** — for your **FortiCNAPP tenant integration**<br><br>📘 For setup instructions and configuration details, see **[Main AWS Folder `README.md`](../README.md)**. |
| **1-2** | 1. Generate the AWS cloud-account integration using the **FortiCNAPP (Lacework) CLI**:<br>` lacework generate cloud-account aws`<br><br>* 2.(Optional)* Specify a profile if you are using multiple:<br>`lacework generate cloud-account aws --profile default` |



<img width="757" height="336" alt="Screenshot 2025-10-20 at 4 05 46 PM" src="https://github.com/user-attachments/assets/ac673228-bd24-4362-b374-e2c0fafd5f96" />  


<img width="973" height="133" alt="Screenshot 2025-10-20 at 4 10 19 PM" src="https://github.com/user-attachments/assets/89992f5e-628d-41b1-9ec4-bdcc79bd1abe" />
