# Day 2: Bootstrapping a Multi‑Node Cluster with kubeadm

## Objectives
- Initialize the Kubernetes control plane on the master node.
- Configure kubeconfig for kubectl access.
- Install Calico CNI plugin for pod networking.
- Join the worker node to the cluster using the kubeadm join command.
- Verify multi‑node cluster status.

---

## Step 1: Initialize Master Node
On the **master node** (`k8s-node1.example.com`):

```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16 --cri-socket=unix:///run/containerd/containerd.sock
```

This sets up the control plane with containerd as the runtime and Calico‑compatible pod CIDR.

---

## Step 2: Configure kubectl Access
Still on the master node:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Verify:
```bash
kubectl get nodes
```

---

## Step 3: Install Calico CNI Plugin
On the master node:

```bash
wget https://raw.githubusercontent.com/projectcalico/calico/v3.28.2/manifests/calico.yaml
kubectl apply -f calico.yaml
```

Check Calico pods:
```bash
kubectl get pods -n kube-system
```

---

## Step 4: Generate Join Command
On the master node, print the join command:

```bash
kubeadm token create --print-join-command
```

Copy the full command (including token and hash).

---

## Step 5: Join Worker Node
On the **worker node** (`k8s-node2.example.com`), run the join command you copied:

```bash
sudo kubeadm join <MASTER_IP>:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
```

---

## Step 6: Verify Cluster
Back on the master node:

```bash
kubectl get nodes
kubectl get pods -A
```

Expected output:
- Both `k8s-node1.example.com` and `k8s-node2.example.com` show `Ready`.
- Calico pods are running in `kube-system`.

---

## Lab Verification
- Control plane initialized successfully on master node.
- kubeconfig configured for kubectl access.
- Calico CNI plugin installed and pods running.
- Worker node joined cluster.
- `kubectl get nodes` shows both nodes as `Ready`.

---

## Next Lab
Proceed to **Day 3: Exploring Kubernetes Cluster Architecture with Diagrams**.

