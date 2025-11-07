


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

## 🧩 Quick Lacework (FortiCNAPP) Agent Health Checks

Use these minimal commands to verify that the Lacework agents on OpenShift are deployed, connected, and collecting telemetry — without dumping hundreds of log lines.

| Check | Command | Expected Output | Meaning |
|--------|----------|----------------|----------|
| **1️⃣ Verify pods are running** | `oc -n lacework get pods -l name=lacework-agent` | All pods show `1/1 Running` and no restarts | ✅ Agents deployed and healthy |
| **2️⃣ Confirm backend connection** | ```bash
POD=$(oc -n lacework get pod -l name=lacework-agent -o jsonpath='{.items[0].metadata.name}')
oc -n lacework logs "$POD" | grep -E -i 'connected to controller|authenticated|registered' | tail -n 3
``` | Lines like:<br>`Connected to controller, version ...` | ✅ Agent authenticated and connected |
| **3️⃣ Check runtime monitoring (eBPF)** | `oc -n lacework logs "$POD" | grep -E -i 'EventMonitor|Loaded eBPF' | tail -n 3` | Shows `Starting Container EventMonitor.....` and `Loaded eBPF programs ...` | ✅ Runtime visibility active |
| **4️⃣ Check for real errors only** | `oc -n lacework logs "$POD" | grep -E -i 'level=error' | tail -n 5` | Ideally **no output** or only single transient `no such process` | ✅ No critical errors |
| **5️⃣ (Optional) Check labels/SCC** | `oc -n lacework describe pod -l name=lacework-agent | egrep -i 'Service Account|scc:|SecurityContext' | head -n 5` | Shows correct service account & SCC | 🧩 Confirms correct OpenShift permissions |

---

### 🧠 Notes
- These `grep` filters show **only the few lines you care about** — connection, runtime, or error status.  
- If step **2** or **3** returns nothing, the agent might not yet be connected (wait 30–60 s).  
- If step **4** prints repeated authentication or connection errors, check:  
  - Lacework API key/secret  
  - Proxy or outbound network access to the Lacework backend  

✅ When all commands above show “Connected”, “EventMonitor”, and no critical `level=error`, your FortiCNAPP agents are fully operational.



October 2025 Linux Agent Releases
https://docs.fortinet.com/document/forticnapp/latest/release-notes/412967/october-2025-linux-agent-releases


Host vulnerability assessment is supported for the following Linux and Windows operating systems, applications, packages, and language libraries.
RHCOS:
https://docs.fortinet.com/document/forticnapp/latest/administration-guide/999307/host-os-and-language-library-support-for-vulnerability-assessment#:~:text=8.x-,Red%20Hat%20Core%20OS,-4.x


