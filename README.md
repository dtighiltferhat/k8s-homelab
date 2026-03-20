# Kubernetes Home Lab — Full Cluster Deployment & Operations

A multi-node Kubernetes cluster built from scratch using kubeadm, covering the full lifecycle: provisioning, workload deployment, Helm-based releases, observability with Prometheus and Grafana, autoscaling, and failure recovery. Ansible handles all node provisioning and configuration management.

## Cluster Architecture

```
┌─────────────────────────────────────────────┐
│              Control Plane Node              │
│  kube-apiserver · etcd · scheduler          │
│  controller-manager · cloud-controller       │
└──────────────────┬──────────────────────────┘
                   │
       ┌───────────┴───────────┐
       │                       │
┌──────▼───────┐       ┌───────▼──────┐
│  Worker 01   │       │  Worker 02   │
│  (app pods)  │       │  (app pods)  │
└──────────────┘       └──────────────┘
```

## Repo Structure

```
k8s-homelab/
├── cluster-setup/          # kubeadm init configs, CNI setup
├── deployments/
│   ├── app/                # Sample app: Deployment, Service, ConfigMap, HPA
│   └── monitoring/         # Prometheus + Grafana manifests
├── helm-charts/            # Custom Helm chart for sample app
├── ansible/
│   ├── roles/
│   │   ├── common/         # Base packages, sysctl, swap disable
│   │   ├── docker/         # Container runtime installation
│   │   └── kubernetes/     # kubeadm, kubelet, kubectl install
│   └── inventory/          # Host inventory files
└── docs/                   # Architecture diagrams, runbooks
```

## Tech Stack

| Tool | Purpose |
|---|---|
| Kubernetes (kubeadm) | Cluster provisioning |
| Docker / containerd | Container runtime |
| Flannel | CNI networking |
| Helm | Application packaging |
| Prometheus | Metrics collection |
| Grafana | Dashboards & visualization |
| Alertmanager | Alert routing |
| Ansible | Node provisioning & config |

## Quick Start

### 1. Provision nodes with Ansible
```bash
cd ansible
ansible-playbook -i inventory/hosts.yml playbooks/provision-nodes.yml
```

### 2. Initialize control plane
```bash
cd cluster-setup
kubeadm init --config kubeadm-config.yaml
```

### 3. Join worker nodes
```bash
# Run the join command output from kubeadm init on each worker node
kubeadm join <control-plane-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

### 4. Deploy monitoring stack
```bash
kubectl apply -f deployments/monitoring/
```

### 5. Deploy sample application
```bash
helm install sample-app helm-charts/sample-app/
```

## Key Concepts Demonstrated

- **kubeadm** cluster setup with custom configuration
- **Namespace isolation** between app and monitoring workloads
- **ConfigMaps and Secrets** for application configuration
- **Horizontal Pod Autoscaler** based on CPU metrics
- **Helm charts** for versioned, repeatable deployments
- **Prometheus scraping** with custom ServiceMonitor resources
- **Grafana dashboards** for pod health and resource utilization
- **Node failure simulation** and recovery procedures

## Author

**Djamal Tighilt Ferhat** — DevOps & Cloud Engineer
[github.com/dtighiltferhat](https://github.com/dtighiltferhat)
