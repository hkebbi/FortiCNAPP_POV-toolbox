


# 1) Create namespace
oc create namespace lacework --dry-run=client -o yaml | oc apply -f -

# 2) Grant SCC 'privileged' to the DEFAULT SA in that namespace
oc -n lacework adm policy add-scc-to-user privileged -z default

helm repo add lacework https://lacework.github.io/helm-charts/
helm repo update

helm upgrade --install lacework-agent lacework/lacework-agent \
  --namespace lacework --create-namespace \
  --set laceworkConfig.accessToken=b8e67xxxxxxxxxxxx2 \
  --set laceworkConfig.serverUrl=https://api.lacework.net \
  --set laceworkConfig.kubernetesCluster=rhcos \
  --set laceworkConfig.env=poc \
  --set priorityClassCreate=true  \
  --set laceworkConfig.codeaware.enable=experimental \
  --set laceworkConfig.packagescan.enable=true \
  --set laceworkConfig.procscan.enable=true \
  --set tolerations[0].operator=Exists


  Check lacework agents:
  oc -n lacework get ds,pods -o wide

  # See labels to confirm
oc -n lacework get pods -o wide --show-labels

# Describe a pod using the right label
oc -n lacework describe pod -l name=lacework-agent | egrep -i 'Service Account|scc:|SecurityContext|Reason'


Quick connectivity check
# show recent connect/auth lines
POD=$(oc -n lacework get pod -l name=lacework-agent -o jsonpath='{.items[0].metadata.name}')
oc -n lacework logs "$POD" | egrep -i 'authenticated|connected|registered|error|fail' | tail -n 80


## 🧩 FortiCNAPP (OpenShift RHCOS) Agent Troubleshooting Guide

---

### 🧱 1. Check Lacework Agents Deployment

| Step | Command | Expected Output | Meaning / Action |
|------|----------|----------------|------------------|
| **List DaemonSet and pods** | `oc -n lacework get ds,pods -o wide` | All pods show `1/1 Running` and DaemonSet desired = current = ready | ✅ Agents deployed successfully across all nodes |
| **Show pod labels** | `oc -n lacework get pods -o wide --show-labels` | Labels include `name=lacework-agent` | ✅ Confirms label selector used by DaemonSet |
| **Describe one agent pod** | `oc -n lacework describe pod -l name=lacework-agent \| egrep -i 'Service Account\|scc:\|SecurityContext\|Reason'` | Shows service account and SCC in use | 🧩 Confirms correct permissions (privileged / SCC assignment) |

---

### 🧱 1. Check Agent Pod Status

| Purpose | Command | Expected Output | Meaning |
|----------|----------|----------------|----------|
| **List Lacework pods** | `oc -n lacework get pods -l name=lacework-agent` | All pods show `1/1 Running` and no restarts | ✅ Agent pods deployed and healthy |
| **Confirm labels (optional)** | `oc -n lacework get pods -o wide --show-labels` | Includes label `name=lacework-agent` | 🧩 Confirms correct DaemonSet labels |

---

### 🌐 2. Verify Connectivity to Backend
#### Change lacework-agent-cpwwt with your pod-name

| Purpose | Command | Example Output | Meaning |
|----------|----------|----------------|----------|
| **Check authentication / controller connection** | `oc -n lacework logs lacework-agent-cpwwt \| grep -E -i 'connected to controller\|authenticated\|registered' \| tail -n 3` | ```time="2025-11-04T07:44:46.897Z" level=info msg="Connected to controller, version 7.10.0.29352"``` | ✅ Agent is authenticated and connected to the Lacework (FortiCNAPP) backend |

---

### ⚙️ 3. Confirm Runtime Monitoring (eBPF)
#### Change lacework-agent-cpwwt with your pod-name

| Purpose | Command | Example Output | Meaning |
|----------|----------|----------------|----------|
| **Check EventMonitor and eBPF status** | `oc -n lacework logs lacework-agent-cpwwt \| grep -E -i 'EventMonitor\|Loaded eBPF' \| tail -n 3` | ```time="2025-11-04T07:44:27.973Z" level=info msg="Loaded eBPF programs for socket events: V2Btf"``` | ✅ Runtime visibility and container telemetry active |

---

### 🚨 4. Look for Real Errors
#### Change lacework-agent-cpwwt with your pod-name

| Purpose | Command | Example Output | Meaning |
|----------|----------|----------------|----------|
| **Show only error lines (last few)** | `oc -n lacework logs lacework-agent-cpwwt \| grep -E -i 'level=error' \| tail -n 5` | ```time="2025-11-04T07:44:50.486Z" level=error msg="Failed to stop child ... no such process"``` | ⚙️ Harmless cleanup error — ignore unless repeated |

---

### 🧠 5. Interpretation Summary

| Result | What It Means | Action |
|---------|----------------|--------|
| `Connected to controller` appears | Agent authenticated and communicating | ✅ Healthy |
| `Starting Container EventMonitor` and `Loaded eBPF` appear | Runtime container monitoring active | ✅ Working |
| Only one or few “no such process” errors | Transient internal cleanup | ⚙️ Normal |
| Repeated `failed to authenticate` or `connection refused` | Network, key, or proxy issue | ❌ Check credentials or outbound access |

---

**✅ Healthy agent confirmation checklist:**
- Pod shows **`Running`**
- Logs include **`Connected to controller`**
- Logs include **`Starting Container EventMonitor` / `Loaded eBPF`**
- No repeating `level=error` lines

If all are true → your Lacework FortiCNAPP agents are **fully operational**.


---

## 📚 Reference Documentation

Use the following official Fortinet FortiCNAPP resources when deploying or troubleshooting the Lacework (FortiCNAPP) Linux agent on OpenShift and related platforms.

| Reference Topic | Description | Official Documentation Link |
|------------------|-------------|------------------------------|
| **Installing on OpenShift / Kubernetes** | Step-by-step installation of the FortiCNAPP (Lacework) Linux agent as a DaemonSet in OpenShift and Kubernetes clusters. Includes YAML examples, RBAC configuration, and verification steps. | [Installing Linux Agent on Kubernetes (FortiCNAPP Admin Guide)](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/663510/installing-linux-agent-on-kubernetes) |
| **October 2025 Linux Agent Releases** | Latest release notes for the FortiCNAPP Linux Agent, including new features, fixes, version numbers, and compatibility information for October 2025. | [October 2025 Linux Agent Release Notes](https://docs.fortinet.com/document/forticnapp/latest/release-notes/412967/october-2025-linux-agent-releases) |
| **Host OS & Language Library Support** | Lists the supported Linux/Windows OS versions, RHCOS compatibility, applications, and language package libraries for vulnerability assessments. | [Host OS and Language Library Support for Vulnerability Assessment (RHCOS 8.x)](https://docs.fortinet.com/document/forticnapp/latest/administration-guide/999307/host-os-and-language-library-support-for-vulnerability-assessment#:~:text=8.x-,Red%20Hat%20Core%20OS,-4.x) |

---


