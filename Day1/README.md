# Day 1: Installing Containerd, kubeadm, kubectl & Helm on Ubuntu 24.04 VM

## Objectives
- Create two Ubuntu 24.04 VMs (one master, one worker).
- Configure hostnames and `/etc/hosts` for cluster communication.
- Install and configure **containerd** as the container runtime.
- Install Kubernetes packages (`kubeadm`, `kubectl`, `kubelet`).
- Initialize the master node and install Calico CNI plugin.
- Generate join command for worker node.

---

## Step 1: Create Two VMs
Provision two Ubuntu 24.04 VMs (via VirtualBox, VMware, cloud provider, or bare metal):

- Master node: `k8s-node1.example.com`
- Worker node: `k8s-node2.example.com`

---

## Step 2: Set Hostnames
On each VM, set the hostname:

**Master:**
```bash
sudo hostnamectl set-hostname k8s-node1.example.com
```

**Worker:**
```bash
sudo hostnamectl set-hostname k8s-node2.example.com
```

Verify:
```bash
hostname
```

---

## Step 3: Update `/etc/hosts`
Add entries on **both VMs** (replace IPs with your VM addresses):

```bash
sudo nano /etc/hosts
```

Example:
```
192.168.56.101   k8s-node1.example.com
192.168.56.102   k8s-node2.example.com
```

---

## Step 4: Run Setup Script
Save the following script as `setup-k8s.sh` and run with `sudo bash setup-k8s.sh` on **both VMs**:

```bash
#!/bin/bash

# Install containerd
apt update && apt -y install containerd

# Configure containerd
mkdir -p /etc/containerd
containerd config default | tee /etc/containerd/config.toml
sed -i 's#\(sandbox_image =\).*#\1 "registry.k8s.io/pause:3.9"#' /etc/containerd/config.toml
sed -i 's/\(SystemdCgroup =\).*false/\1 true/' /etc/containerd/config.toml
systemctl restart containerd.service
systemctl enable containerd.service

# Sysctl settings
cat > /etc/sysctl.d/99-k8s-cri.conf <<EOF
net.bridge.bridge-nf-call-iptables=1
net.bridge.bridge-nf-call-ip6tables=1
net.ipv4.ip_forward=1
EOF
sysctl --system

# Kernel modules
modprobe overlay
modprobe br_netfilter
echo -e "overlay\nbr_netfilter" > /etc/modules-load.d/k8s.conf

# Disable swap
swapoff -a
sed -i '/ swap / s/^/#/' /etc/fstab

# Install Kubernetes packages
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /" | tee /etc/apt/sources.list.d/kubernetes.list
apt update
apt -y install kubeadm kubelet kubectl
apt-mark hold kubeadm kubelet kubectl

# Master node initialization
if [[ "$(hostname)" == "k8s-node1.example.com" ]]; then
    kubeadm init --pod-network-cidr=192.168.0.0/16 --cri-socket=unix:///run/containerd/containerd.sock

    mkdir -p $HOME/.kube
    cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    chown $(id -u):$(id -g) $HOME/.kube/config

    # Install Calico CNI plugin (v3.28.2)
    wget https://raw.githubusercontent.com/projectcalico/calico/v3.28.2/manifests/calico.yaml
    kubectl apply -f calico.yaml

    # Verify cluster status
    kubectl get nodes
    kubectl get pods -A

    # Print join command for worker node
    kubeadm token create --print-join-command
fi

echo "Node setup is complete."
```

---

## Step 5: Join Worker Node
- Copy the join command printed by the master node.
- Run it on the worker node:
```bash
sudo kubeadm join <MASTER_IP>:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
```

---

## Step 6: Verify Cluster
On the master node:
```bash
kubectl get nodes
kubectl get pods -A
```

Expected output:
- Both `k8s-node1.example.com` and `k8s-node2.example.com` show `Ready`.
- Calico pods are running in `kube-system`.

---

## Lab Verification
- Two VMs created and hostnames set.
- `/etc/hosts` updated for name resolution.
- Containerd configured with `pause:3.9` and `SystemdCgroup=true`.
- Master initialized with kubeadm and Calico installed.
- Worker joined successfully.
- Cluster shows both nodes as `Ready`.

---

## Next Lab
Proceed to **Day 2: Bootstrapping a Multi‑Node Cluster with kubeadm**.
```


This README now includes **VM creation, hostname setup, `/etc/hosts` entries, and your script** in a clean step‑by‑step format.  

👉 Do you want me to prepare **Day 2 README.md** next (worker join + cluster validation expanded), so your repo has continuity?
