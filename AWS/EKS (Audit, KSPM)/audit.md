# 🧩 AWS-EKS Audit Integration  

## ⚙️ Definition




## 📋 EKS Audit Logging Requirement

| **Requirement**   | **Description / Steps**                                                                                       |
| ----------------- | ------------------------------------------------------------------------------------------------------------- |
| **Audit Logging** | Must be enabled on all EKS clusters that you plan to integrate with FortiCNAPP.                               |
| **Purpose**       | EKS Audit Logs capture control-plane API calls, enabling continuous anomaly detection and security analytics. |




## 🚀 Terraform Integration Options

| **Option**                                        | **Description / Steps**                                                                                                                                                                                                                                                                                   | **Best For**                                                     |
| ------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| **Option 1 — UI-Based Integration (Recommended)** | 1. Go to **Settings → Cloud Accounts → Add New → AWS**<br>2. Choose **Integration Type → EKS Audit Log**<br>3. Select AWS region, EKS clusters, and API key<br>4. Click **Generate CLI Bundle**<br>5. Choose **CloudShell** or supported host<br>6. Copy & paste the generated command into your terminal | ✅ Simplified setup via UI and guided configuration               |
| **Option 2 — CLI-Based Integration (Terraform)**  | Run:<br>`bash<br>lacework generate k8s eks<br>`<br>Follow prompts:<br>`\n▸ Load cached values? No\n▸ Integrate in multiple regions? No\n▸ Specify AWS region: eu-central-1\n▸ Clusters: hkeksfrankfurt\n▸ Advanced options? No\n▸ Run Terraform plan? yes\n`                                              | ⚙️ Full control via CLI; suited for automation and IaC workflows |
