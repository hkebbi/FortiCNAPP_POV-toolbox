# 🚀 How AWS Cloud API Integration (CSPM, CIEM, UEBA) Is Deployed ?
## 🔧 AWS Cloud Account Configuration Workflow (FortiCNAPP) Using FortiCNAP CLI


| Step | Description |
|------|-------------|
| **Overview** | In this setup, **Terraform** is used via the **FortiCNAPP CLI** to deploy a **multi-regional, single-account** or **multi-account** environment. |
| ⚠️ **Pre-Deployment Note** | Make sure you have both:<br>🟦 **AWS Profile** — for your **AWS account integration**<br>🟩 **FortiCNAPP (Lacework) Profile** — for your **FortiCNAPP tenant integration**<br><br>📘 For setup instructions and configuration details, see **[Main AWS Folder `README.md`](../README.md)**. |
| **1-2** | 1. Generate the AWS cloud-account integration using the **FortiCNAPP (Lacework) CLI**:<br>` lacework generate cloud-account aws`<br><br>* 2.(Optional)* Specify a profile if you are using multiple:<br>`lacework generate cloud-account aws --profile default` |



<img width="757" height="336" alt="Screenshot 2025-10-20 at 4 05 46 PM" src="https://github.com/user-attachments/assets/ac673228-bd24-4362-b374-e2c0fafd5f96" />  


<img width="973" height="133" alt="Screenshot 2025-10-20 at 4 10 19 PM" src="https://github.com/user-attachments/assets/89992f5e-628d-41b1-9ec4-bdcc79bd1abe" />
