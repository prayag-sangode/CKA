# Day 3: Exploring Kubernetes Cluster Architecture with Diagrams

## Objectives
- Understand the **control plane components** (API Server, etcd, Scheduler, Controller Manager).
- Explore **worker node components** (kubelet, kube‑proxy, container runtime).
- Visualize how requests flow through the cluster.
- Verify architecture using `kubectl` commands.

## Step 1: Explore Control Plane Components
On the master node, list system pods:
```bash
kubectl get pods -n kube-system -o wide
```

You should see:
- `kube-apiserver-<node>`
- `etcd-<node>`
- `kube-scheduler-<node>`
- `kube-controller-manager-<node>`

These are the brains of the cluster.

## Step 2: Explore Worker Node Components
On the worker node, check running processes:
```bash
ps -ef | grep kubelet
ps -ef | grep kube-proxy
```

Worker node runs:
- **kubelet** → ensures pods are running.
- **kube-proxy** → handles networking and service routing.
- **container runtime (containerd)** → runs containers inside pods.

## Step 3: Verify Cluster Nodes
```bash
kubectl get nodes -o wide
```
Shows master and worker nodes, their roles, and status.

## Step 4: Deploy a Test Workload
```bash
kubectl create deployment nginx --image=nginx --replicas=2
kubectl get pods -o wide
```

Observe how pods are scheduled across worker nodes by the **scheduler**.


## Step 5: Visualize Cluster Architecture

**Control Plane:**
- API Server → entry point for all requests.
- etcd → stores cluster state.
- Scheduler → decides which node runs a pod.
- Controller Manager → enforces desired state.

**Worker Nodes:**
- kubelet → manages pods.
- kube‑proxy → handles networking.
- container runtime → runs containers.

Communication flows:
1. `kubectl` → API Server.
2. API Server → etcd (state storage).
3. Scheduler → assigns pods to nodes.
4. kubelet → runs pods via container runtime.
5. kube‑proxy → exposes services.


## Step 6: Troubleshooting Exercise
Stop kubelet on worker node:
```bash
sudo systemctl stop kubelet
```

On master:
```bash
kubectl get nodes
```

Worker node will show **NotReady**. Pods may reschedule to other nodes.


## Lab Verification
- You listed control plane pods in `kube-system`.
- You identified worker node processes (`kubelet`, `kube-proxy`, containerd).
- You deployed workloads and observed scheduling.
- You visualized how control plane and worker nodes interact.
- You tested failure by stopping kubelet and saw cluster reaction.


## Next Lab
Proceed to **Day 4: Managing Clusters Using kubectl and kubeconfig**.
