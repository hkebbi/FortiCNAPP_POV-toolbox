## 🛡️ EKS agent Deployment Using Helm Charts
- Use **Helm** for easy deployment and lifecycle management
Helm helps you manage Kubernetes applications — Helm Charts help you define, install, and upgrade even the most complex Kubernetes application.
- Helm is a package manager for K8s that uses a packaging format called charts. A chart is a collection of files that describe a related set of K8s resources. 

For the Lacework FortiCNAPP charts:
Helm acts as the installer and lifecycle manager: The DaemonSet is the actual Kubernetes workload that ensures one agent pod runs on every node.
So, in essence: Helm → installs and manages → DaemonSet → runs agents on every node.
 - A DaemonSet is a Kubernetes controller that ensures a specific Pod runs on every (or selected) node in your cluster.
   
* Note: Fargate does not support Daemonset. So, the only way to monitor an application running on Fargate is by embedding the agent in the application at image build time or by injecting a sidecar into the pod.


---
#### ✅ 1. Create New Access Token

- Lacework FortiCNAPP Console, go to Settings > Configuration > Agent Tokens.
- Add Name and Description.
- Select Linux as the Operating System.
- Click Save to create your new access token for Linxu agent.
* An access token can be re-used for multiple agent installations.


#### ✅ 2. Deploy Helm

```bash
helm repo add lacework https://lacework.github.io/helm-charts/
helm repo update
```

#### ✅ 3.1. Method-1. Deploy Agent Using CLI:

```bash
helm upgrade --install lacework-agent lacework/lacework-agent \
  --namespace lacework --create-namespace \
  --set laceworkConfig.accessToken=2f2abcdefghijklmnopqr \
  --set laceworkConfig.serverUrl=https://aaa-eu.lacework.net \
  --set laceworkConfig.kubernetesCluster=hkeksfrankfurt \
  --set laceworkConfig.env=poc \
# --set laceworkConfig.proxyUrl=http://proxy.example:3128 \
  --set 'tolerations[0].operator=Exists'
```


#### ✅ 3.2. Method-2. Deploy Agent Using .yaml file:

```bash
cat values.yaml 
laceworkConfig:
  serverUrl: https://aaa-eu.lacework.net
  kubernetesCluster: EKSclustername
  env: poc
# proxyUrl: http://your.proxy:3128     # <— uncomment to enable
# Keep toleration so it schedules on tainted nodes
tolerations:
  - operator: Exists
```

```bash
cat values-secrets.yaml 
laceworkConfig:
  accessToken: "2abcdefghijklmnopqr"
```

```bash
helm upgrade --install lacework-agent lacework/lacework-agent \
  -n lacework --create-namespace \
  -f values.yaml -f values-secrets.yaml
```


