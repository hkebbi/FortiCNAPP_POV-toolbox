## 🛡️ EKS agent Deployment Using Helm Charts
- Use **Helm** for easy deployment and lifecycle management

- Helm → A packaging and lifecycle management tool that can create DaemonSets, Deployments, Services, etc.
- DaemonSet → The actual Kubernetes resource that runs one pod per node.
For the Lacework FortiCNAPP charts:

So, in essence: Helm → installs and manages → DaemonSet → runs agents on every node.

- A Helm chart is a folder that contains all the YAML templates and configuration files needed to deploy an application on Kubernetes.
* Note: Fargate does not support Daemonset. So, the only way to monitor an application running on Fargate is by embedding the agent in the application at image build time or by injecting a sidecar into the pod.

| **Concept**          | **Description**                                                                 |
| -------------------- | ------------------------------------------------------------------------------- |
| **Helm**             | A package manager for Kubernetes applications.                                  |
| **Helm Chart**       | A collection of files (YAML templates + configs) that define a deployable app.  |
| **Chart.yaml**       | Describes the chart (name, version, dependencies).                              |
| **values.yaml**      | Default configuration values for the templates.                                 |
| **templates/*.yaml** | Kubernetes resource templates (DaemonSet, Deployment, Service, etc.).           |
| **Rendered YAML**    | What Helm produces after substituting all the template variables.               |
| **Install Command**  | `helm install <release-name> <chart-path>` — deploys everything to the cluster. |

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


