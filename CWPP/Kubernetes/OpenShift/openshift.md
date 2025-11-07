


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


## 🧩 FortiCNAPP (Lacework) Agent Troubleshooting Guide

| Step | Command | Expected Output | Meaning | Action |
|------|----------|----------------|----------|---------|
| **1. Check agent pods status** | `oc -n lacework get pods -l name=lacework-agent` | All pods show `1/1 Running` and `STATUS=Running` | Agents are deployed and healthy across all nodes | ✅ No action required |
| **2. Verify agent connection to backend** | `oc -n lacework logs "<agent-pod>" \| egrep -i 'connected|authenticated|registered' \| tail -n 10` | `Connected to controller`, `Authenticated`, or `Registered` | Agent successfully authenticated and connected to FortiCNAPP SaaS | ✅ Working as expected |
| **3. Check for runtime event collection** | `oc -n lacework logs "<agent-pod>" \| egrep -i 'upload|event'` | Lines like `Starting Container EventMonitor.....`, `Loaded eBPF programs for socket events` | Runtime eBPF monitors are active for process/network telemetry | ✅ Agent actively monitoring containers |
| **4. Investigate “rpmdb.sqlite” or “Packages.db” messages** | (From agent logs) `CAA: handled operation ... found error: open ... no such file or directory` | Informational only (`level=info`) | Some containers don’t use RPM-based package managers (e.g., Alpine, Debian) | ⚙️ Ignore – normal behavior |
| **5. Identify true errors** | Search for `level=error` in logs | e.g., `failed to authenticate`, `connection refused`, `registration failed` | Connectivity, key, or proxy issue | ❌ Review access key, proxy, or outbound network configuration |
| **6. Confirm telemetry transmission** | `oc -n lacework logs "<agent-pod>" \| egrep -i 'upload|heartbeat'` | Lines like `uploading results` or `sending heartbeat` | Agent successfully transmitting scan data to backend | ✅ Confirmed operational |
| **7. Optional cleanup** | Remove unexpected images (e.g., `bkimminich/juice-shop`) via FortiCNAPP UI | Asset disappears from “Container Images” list | Keeps scan inventory restricted to desired repos | 🧹 Optional hygiene step |

---

### ✅ Interpretation Summary

| Indicator | Example Log Snippet | Status |
|------------|---------------------|---------|
| `Connected to controller` | Agent connected to backend | 🟢 Working |
| `Authenticated / Registered` | Successfully registered with platform | 🟢 Working |
| `CAA: handled operation ... no such file` | Missing RPM DB (expected) | 🟢 Normal |
| `Starting Container EventMonitor` | Runtime monitoring active | 🟢 Active |
| `failed to authenticate / connection refused` | Network or credentials issue | 🔴 Needs investigation |

---

### 🧠 Notes
- `CAA` = Container Active Analysis (package inventory subsystem)  
- eBPF logs confirm runtime detection is active (`Loaded eBPF programs for socket events`)  
- If no `level=error` lines appear, the agent is functioning normally.  
- Agents reconnect automatically after network interruptions.  
- For stricter filtering, disable *Auto-Discovery* in the Docker Hub connector and explicitly define your target repositories.

