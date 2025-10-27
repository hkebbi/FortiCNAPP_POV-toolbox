# 🧩 AWS-EKS Audit Integration  

## ⚙️ Definition  

| **Capability / Feature**                       | **Description**                                                                                                                                                                    |
| ---------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Enhanced Kubernetes Security Visibility**    | FortiCNAPP extends **anomaly detection** and **behavioral analytics** into **Amazon EKS clusters**, providing deep visibility into cluster activity and control-plane operations.  |
| **EKS Audit Log Integration**                  | Integrates directly with **AWS-managed EKS Audit Logs** to continuously monitor and detect **unusual activity**, **risky configurations**, and **potential threats** in real time. |
| **🔎 Deep Audit Visibility**                   | Provides comprehensive visibility into **EKS control-plane API events**, including user actions, resource modifications, and access attempts.                                      |
| **🤖 ML-Based Anomaly Detection**              | Uses **machine learning** to automatically identify **deviations from normal behavior**, reducing manual investigation time.                                                       |
| **👥 User & Entity Behavior Analytics (UEBA)** | Leverages **UEBA** to correlate and analyze user activity patterns, reducing false positives and surfacing only true security anomalies.                                           |




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
