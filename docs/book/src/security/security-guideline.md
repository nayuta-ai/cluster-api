# Spoofing
## STRIDE-SPOOF-1 - Spoofing of Cloud Admin
Securely manage Cloud and Kubernetes Credentials with following methods applied:
- Second pair of eyes are applied when executing privileged actions such as creating/deleting/updating a cluster
- Enable auditing on the management cluster as well as on the cloud provider
```
apiVersion: audit.k8s.io/v1
kind: Policy
rules:
- level: Metadata
  resources:
  - group: "cluster.x-k8s.io"
    resources: ["clusters", "machines"]
  verbs: ["create", "delete", "update"]
```
ref: https://kubernetes.io/docs/tasks/debug/debug-cluster/audit/

# Tampering
## STRIDE-TAMPER-1 Tampering of Cluster API component binaries and configuration files
- During runtime: Alert on modification or restarts of cluster API components to detect for potential tampering.

## STRIDE-TAMPER-2 Tampering of Machine Images
- During runtime: Disable or restrict SSH access to prevent updates to machine image at runtime. Create alerting for sensitive file systems and when the machine is restarted.

# Repudiation
## STRIDE-REPUDIATE-01 Repudiation of actions performed by Cloud Admin
- Implement API server auditing for all clusters and Cloud API auditing to log all actions performed by Infra Admins. Additionally, implement centralized audit collection and alerting for any suspicious activity.
## STRIDE-REPUDIATE-02 Repudiation of actions performed using cloud credentials
- Recommended Mitigation: Implement Cloud API auditing to log all actions performed using cloud credentials granted to the management and workload cluster’s control plane. Additionally, implement centralized audit collection and alerting for any suspicious activity.

# Information Disclosure
## STRIDE-INFODISCLOSE-1 Privilege escalation from workload cluster co-located with management cluster
- Strongly advise users to seperate workload clusters into different AWS accounts or Azure subscriptions.

## STRIDE-INFODISCLOSE-2 Exposure of kubeadm token credentials
- Disable or restrict SSH access to nodes in a cluster
- Audit KubeConfig files access, edits that are located on the node

## STRIDE-INFODISCLOSE-3 - Exposure of Management and Workload Cluster CA private keys and certs
- Disable or restrict SSH access to nodes in a cluster
- Audit access or edits to CA private keys and cert files located on the node

## STRIDE-INFODISCLOSE-4 - Exposure of Cloud credentials that allow privileged actions
- Disable or restrict SSH access to nodes in a cluster

# Denial of Service
## STRIDE-DOS-2 - Misuse of Infra Admin credentials to create unlimited number of clusters
- Apply second pair of eyes principle when performing privilege actions such as creating a cluster by an Infra admin
## STRIDE-DOS-3 - Misuse of Infra Admin credentials to create unlimited number of Nodes
- Apply second pair of eyes principle when performing privilege actions such as creating a node by an Infra admin
## STRIDE-DOS-5 - Deleting ELB instance for API server of workload cluster to disconnect management cluster from workload cluster(s)
- Alert on deletion of Elastic Lb so that it can be quickly restored
## STRIDE-DOS-6 Exhausting Cloud resources
- Apply second pair of eyes whenever possible for such activity
- Apply alerting on all cloud resource creation, update, deletion activities

# Elevation of Privilege
## STRIDE-EOP-1 Using workload cluster credentials to get access to mgmt. Cluster
- Separate workload cluster and management cluster at network level through VPC boundary
- Disable or limit node level SSH access
- Do not build a chain of trust for cluster CAs such that gaining access to workload cluster CA and private keys does not provide access to management cluster
- Limit who can create pod on control plane nodes.

## STRIDE-EOP-2 Using create pod permission to get cluster admin access to workload cluster

- Limit who can create pod on control plane nodes
- Disable or limit node level SSH access
## STRIDE-EOP-3 Use shared cloud credentials to get access to other unrelated cloud resources
- Create dedicated cloud credentials that have only the necessary permissions to manage lifecycle of a cluster
