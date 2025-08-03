# k8s
k8 labs on local 




Update on K8s labs 
| Phase | What You'll Do                            | What You'll Learn                     |
| ----- | ----------------------------------------- | ------------------------------------- |
| 1     | Set up a 3-node K8s cluster (VMs)         | kubeadm, kubelet, etcd, control plane |
| 2     | Break & Fix Labs                          | Hands-on troubleshooting              |
| 3     | Deploy Nginx App                          | Pod, Deployment, Service              |
| 4     | Expose App with Ingress                   | Ingress controller, DNS               |
| 5     | Add Monitoring                            | Prometheus, Grafana                   |
| 6     | Handle Secrets & Configs                  | Secrets, ConfigMap                    |
| 7     | Upgrade Cluster                           | Upgrade process                       |
| 8     | CI/CD Setup                               | GitHub Actions → Deploy on cluster    |
| 9     | Advanced: Volumes, StatefulSets, CronJobs | Real production examples              |




# Kubernetes Labs: Local Setup & Practice Guide

## Phase 1: Cluster Setup

### Lab 1: Setup Kubernetes Cluster with kubeadm

* **Goal**: Build a 3-node cluster using VMs (VirtualBox + Ubuntu)
* **Learn**: Cluster bootstrap, kubelet, CNI (Calico), joining nodes

#### Setup Checklist

##### 1. System Preparation (on all nodes)

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl gnupg2 apt-transport-https ca-certificates software-properties-common
```

##### 2. Disable Swap (required for kubelet)

```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

##### 3. Load Kernel Modules and Set Sysctl Params

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

sudo sysctl --system
```

##### 4. Install containerd

```bash
sudo apt install -y containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
```

Edit `/etc/containerd/config.toml`:

```toml
SystemdCgroup = true
```

```bash
sudo systemctl restart containerd
sudo systemctl enable containerd
```

##### 5. Install Kubernetes

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

##### 6. Initialize Kubernetes Cluster (on master node)

```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

##### 7. Setup kubeconfig for current user

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

##### 8. Install Calico CNI

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/calico.yaml
```

##### 9. Allow Pods on Control Plane (for single-node setup)

```bash
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```

##### 10. Test Setup

```bash
kubectl get nodes
kubectl get pods -A
```

##### 11. Create Namespace & Pods

```bash
kubectl create ns test-pod
kubectl apply -f pod-a.yaml -n test-pod
kubectl apply -f pod-b.yaml -n test-pod
kubectl get pods -n test-pod -o wide
```

##### 12. Debug Pod Communication

```bash
kubectl exec -n test-pod pod-a -- ping -c 4 pod-b
kubectl exec -n test-pod pod-a -- nslookup pod-b
```

---

## Phase 2: Core Kubernetes Resources

### Lab 2: Pods, ReplicaSets, Deployments

* Deploy nginx with 3 replicas
* Scale up/down, restart pods
* Use `kubectl explain`, `describe`
* Break it: Force delete pods and watch auto-heal

### Lab 3: Services & DNS

* Create ClusterIP, NodePort services
* Deploy multi-container pods
* Test DNS with curl
* Break it: Delete CoreDNS and debug

### Lab 4: Namespaces, Labels & Selectors

* Create namespaces for dev, staging
* Label/filter with selectors
* Break it: Wrong namespace deployment

---

## Phase 3: Configuration & Secrets

### Lab 5: ConfigMaps & Secrets

* Use ConfigMap for config
* Inject Secrets via env/volume
* Break it: Use wrong key in mount

### Lab 6: ServiceAccounts, RBAC

* Create limited ServiceAccounts
* Test with `kubectl auth can-i`
* Break it: Revoke access and debug

---

## Phase 4: Networking & Ingress

### Lab 7: CNI Plugin & Networking

* Use `calicoctl`/`cilium` to inspect
* Block traffic with NetworkPolicy

### Lab 8: Ingress Controller with TLS

* Use NGINX Ingress
* Route with host/path
* Add TLS via cert-manager
* Break it: Misconfig ingress, debug 404

---

## Phase 5: Storage & StatefulSets

### Lab 9: Persistent Volumes & PVCs

* Deploy MySQL with PVC
* Break it: Delete PVC, watch failure

### Lab 10: StatefulSets

* Deploy Redis/Cassandra
* Observe stable hostname/storage
* Break it: Kill pod, see recovery

---

## Phase 6: Advanced Concepts

### Lab 11: Horizontal Pod Autoscaler

* Use metrics-server
* Stress app via `hey`, `ab`
* Break it: Remove metrics-server

### Lab 12: Taints, Tolerations, Affinity

* Use taints, node affinity
* Break it: Unschedulable pods

### Lab 13: Helm Charts

* Install Prometheus via Helm
* Make custom Helm chart
* Break it: Wrong `values.yaml`

---

## Phase 7: Observability & Logging

### Lab 14: Prometheus + Grafana

* Use kube-prometheus stack
* Create alerts/dashboards
* Break it: Kill node, see alert

### Lab 15: Centralized Logging

* Use Loki or EFK
* View logs via Grafana/Kibana
* Break it: Stop Fluentd/Promtail

---

## Phase 8: Real-World Deployments

### Lab 16: WordPress + MySQL

* Use PVCs, Ingress
* Test data persistence
* Break it: Delete MySQL pod

### Lab 17: CI/CD Integration

* GitHub Actions → `kubectl apply`/Helm
* Break it: Bad commit, rollback

---

## Phase 9: Backup, Upgrade, Chaos

### Lab 18: Velero Backup

* Backup namespaces, PVCs
* Simulate failure, restore

### Lab

