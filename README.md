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



I

If you're setting up a Kubernetes cluster (master node):
Install prerequisites:


sudo apt update && sudo apt install -y apt-transport-https ca-certificates curl
Disable swap (required for Kubernetes):

sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
------------------------------------------------------------------------------------------------------------

Install Docker (or containerd):
🐳 Step-by-Step Docker Installation on Ubuntu 22.04 / 24.04
✅ Step 1: Update and install dependencies
bash
Copy
Edit
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release
✅ Step 2: Add Docker’s official GPG key
bash
Copy
Edit
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
✅ Step 3: Set up Docker repository
bash
Copy
Edit
echo \
  "deb [arch=$(dpkg --print-architecture) \
  signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
✅ Step 4: Install Docker Engine
bash
Copy
Edit
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io
✅ Step 5: Start and enable Docker
bash
Copy
Edit
sudo systemctl enable docker
sudo systemctl start docker
✅ Step 6: (Optional) Allow current user to run Docker without sudo
bash
Copy
Edit
sudo usermod -aG docker $USER
⚠️ You’ll need to log out and log back in for this to take effect.

✅ Step 7: Verify installation
bash
Copy
Edit
docker version
docker run hello-world

-----------------------------------------------------------------------------------------------


1. Turn off swap (K8s requires swap to be off)
bash
Copy
Edit
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
2. Install required packages
bash
Copy
Edit
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
3. Add Kubernetes GPG key
bash
Copy
Edit
sudo curl -fsSLo /etc/apt/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
4. Add Kubernetes apt repo
bash
Copy
Edit
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | \
sudo tee /etc/apt/sources.list.d/kubernetes.list > /dev/null
5. Install Kubernetes components
bash
Copy
Edit
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker

Install kubeadm, kubelet, kubectl:
Follow this official guide: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/



PHASE 1: Cluster Setup (Real Environment)
🔹 Lab 1: Setup Kubernetes Cluster with kubeadm
Goal: Build a 3-node cluster using VMs

Tools: VirtualBox + Ubuntu + kubeadm + Calico

What You’ll Learn: Cluster bootstrap, kubelet, CNI, joining nodes

🛠️ Break it: Delete /etc/kubernetes/manifests/kube-apiserver.yaml and recover it.

🔧 PHASE 2: Core Kubernetes Resources
🔹 Lab 2: Pods, ReplicaSets, Deployments
Create a Deployment (nginx) with 3 replicas

Scale up/down, restart pods

Use kubectl explain and describe

Break it: Force delete all pods and watch self-healing

🔹 Lab 3: Services & DNS
Create ClusterIP, NodePort

Deploy multi-container pod (frontend/backend) with Service discovery

Curl across services to test DNS

Break it: Delete CoreDNS pods and troubleshoot DNS failure

🔹 Lab 4: Namespaces, Labels & Selectors
Create multiple namespaces for dev, staging

Label resources and filter with selectors

Break it: Deploy in wrong namespace and debug "app not working"

🔐 PHASE 3: Configuration & Secrets
🔹 Lab 5: ConfigMaps & Secrets
Externalize app config using ConfigMap

Inject DB credentials via Secret (env & volume)

Break it: Mount incorrect secret key in volume

🔹 Lab 6: ServiceAccounts, RBAC
Create custom ServiceAccount with limited permissions

Test access using kubectl auth can-i

Break it: Revoke access and debug “forbidden” error

🌐 PHASE 4: Networking & Ingress
🔹 Lab 7: CNI Plugin & Pod Networking
Use calicoctl or cilium to inspect traffic rules

Ping between pods across nodes

Break it: Block traffic between pods using Calico NetworkPolicy

🔹 Lab 8: Ingress Controller with TLS
Deploy NGINX Ingress Controller

Route 2 apps on same IP via host/path

Add TLS using cert-manager and self-signed certs

Break it: Misconfigure ingress and debug “404 Not Found”

💾 PHASE 5: Storage & StatefulSets
🔹 Lab 9: Persistent Volumes & Claims
Deploy MySQL with PVC

Use hostPath or OpenEBS if available

Break it: Delete PVC and watch MySQL fail

🔹 Lab 10: StatefulSets
Deploy Cassandra or Redis with StatefulSet

Inspect stable hostnames and storage retention

Break it: Kill one pod and see recovery process

🧠 PHASE 6: Advanced Concepts
🔹 Lab 11: Horizontal Pod Autoscaler
Deploy CPU-based autoscaling using metrics-server

Stress app using hey or ab

Break it: Remove metrics-server and observe no scaling

🔹 Lab 12: Taints, Tolerations, Node Affinity
Create tainted nodes, schedule pods using toleration

Assign pods to zone-specific nodes

Break it: Remove toleration and see unschedulable pods

🔹 Lab 13: Helm Charts
Install Prometheus using Helm

Create custom Helm chart for your app

Break it: Pass wrong values.yaml and debug deployment

📊 PHASE 7: Observability & Logging
🔹 Lab 14: Prometheus + Grafana Monitoring
Deploy kube-prometheus stack

Setup dashboards and alerts

Break it: Kill a node and observe alert triggering

🔹 Lab 15: Centralized Logging with Loki or EFK
Forward logs to Loki or Elasticsearch

Query logs via Grafana or Kibana

Break it: Stop Fluentd or Promtail and verify logs not collected

⚙️ PHASE 8: Real-World Deployments
🔹 Lab 16: WordPress + MySQL
Deploy WordPress + MySQL with persistent volumes

Expose with Ingress, enable scaling

Break it: Delete MySQL pod and observe data persistence

🔹 Lab 17: CI/CD Integration
Create GitHub Action to auto-deploy app to k8s

Use kubectl apply or Helm in pipeline

Break it: Commit a broken manifest and rollback

🔁 PHASE 9: Backup, Upgrade, Disaster Recovery
🔹 Lab 18: Backup Cluster with Velero
Backup namespace, PVCs, etc.

Delete & restore workloads

Break it: Simulate node failure and recover

🔹 Lab 19: Upgrade Kubernetes Safely
Upgrade from v1.29 to v1.30 using kubeadm

Drain node, upgrade, uncordon

Break it: Upgrade without draining and fix impact

🔹 Lab 20: Chaos Engineering (Simulate Failures)
Use chaos-mesh or manual failures (kill kubelet, delete etcd pod)

Monitor system behavior and recovery

🧰 Tools You’ll Use Throughout
kubectl, kubeadm, kustomize, helm

prometheus, grafana, loki, fluentd

minio, velero

cilium, calico

kind, k9s, lens
