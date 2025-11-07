


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

## 🧩 FortiCNAPP (Lacework) Agent Troubleshooting

Use the following steps to verify that the Lacework (FortiCNAPP) agents are deployed correctly, connected to the backend, and monitoring containers as expected.

---

### 🧱 1. Check Lacework Agents Deployment

| Step | Command | Expected Output | Meaning / Action |
|------|----------|----------------|------------------|
| **List DaemonSet and pods** | `oc -n lacework get ds,pods -o wide` | All pods show `1/1 Running` and DaemonSet desired = current = ready | ✅ Agents deployed successfully across all nodes |
| **Show pod labels** | `oc -n lacework get pods -o wide --show-labels` | Labels include `name=lacework-agent` | ✅ Confirms label selector used by DaemonSet |
| **Describe one agent pod** | `oc -n lacework describe pod -l name=lacework-agent \| egrep -i 'Service Account\|scc:\|SecurityContext\|Reason'` | Shows service account and SCC in use | 🧩 Confirms correct permissions (privileged / SCC assignment) |

---

### 🌐 2. Quick Connectivity and Authentication Check

| Step | Command | Expected Output | Meaning / Action |
|------|----------|----------------|------------------|
| **Show recent connect/auth events** | ```bash
POD=$(oc -n lacework get pod -l name=lacework-agent -o jsonpath='{.items[0].metadata.name}')
oc -n lacework logs "$POD" \| egrep -i 'authenticated\|connected\|registered\|error\|fail' \| tail -n 80
``` | `Connected to controller`, `Authenticated`, or `Registered` | ✅ Agent is communicating with the FortiCNAPP backend |
| **Check for runtime event monitor startup** | `oc -n lacework logs "$POD" \| egrep -i 'EventMonitor\|eBPF'` | `Starting Container EventMonitor.....` and `Loaded eBPF programs` | ✅ Runtime container monitoring active |
| **Search for real errors** | `oc -n lacework logs "$POD" \| egrep -i 'level=error'` | No repeated authentication or network errors | ❌ If errors appear, check keys, proxy, or network access |

---

### 🧰 3. Package Scanning Messages

| Message Example | Meaning | Action |
|------------------|----------|---------|
| `CAA: handled operation ... found error: open /usr/lib/sysimage/rpm/Packages: no such file` | Container doesn’t use RPM (e.g., Alpine, Debian) | ✅ Normal informational log, ignore |
| `failed to authenticate / connection refused / registration failed` | Connectivity or credential problem | ❌ Check FortiCNAPP API key, proxy settings, or outbound network |
| `Connected to controller, version x.x.x` | Successful authentication and backend connection | ✅ Agent fully operational |

---

### 🧠 Quick Reference: Healthy Agent Indicators

| Indicator | Example Log Snippet | Status |
|------------|---------------------|---------|
| Connection established | `Connected to controller` | 🟢 Working |
| Authentication complete | `Authenticated / Registered` | 🟢 Working |
| Runtime monitor active | `Starting Container EventMonitor` | 🟢 Active |
| Package scan info logs | `CAA: handled operation ... no such file` | 🟢 Normal |
| Network/auth errors | `failed to authenticate / connection refused` | 🔴 Needs attention |

---

### ✅ Summary

If you see:
- Pods in **Running** state  
- Logs showing **“Connected to controller”**, **“Authenticated”**, and **no `level=error` lines**  
then your Lacework / FortiCNAPP agents are **fully operational**.

Otherwise, check:
- API keys and proxy configuration  
- SCC/permissions in OpenShift  
- Outbound connectivity to the Lacework backend

---





October 2025 Linux Agent Releases
https://docs.fortinet.com/document/forticnapp/latest/release-notes/412967/october-2025-linux-agent-releases


Host vulnerability assessment is supported for the following Linux and Windows operating systems, applications, packages, and language libraries.
RHCOS:
https://docs.fortinet.com/document/forticnapp/latest/administration-guide/999307/host-os-and-language-library-support-for-vulnerability-assessment#:~:text=8.x-,Red%20Hat%20Core%20OS,-4.x


