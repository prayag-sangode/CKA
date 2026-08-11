# Lab 6: Understanding Control Plane Components (API Server, etcd, Scheduler, Controller Manager)

## Objectives
- Explore the Kubernetes **control plane** components.
- Understand the role of each component in maintaining cluster state.
- Inspect system pods running in the `kube-system` namespace.
- Run hands‑on commands to interact with API Server, etcd, Scheduler, and Controller Manager.


## Step 1: List Control Plane Pods
On the master node:
```bash
kubectl get pods -n kube-system -o wide
```

You should see pods like:
- `kube-apiserver-<node>`
- `etcd-<node>`
- `kube-scheduler-<node>`
- `kube-controller-manager-<node>`


## Step 2: Explore API Server
The **API Server** is the front door to the cluster.
- All `kubectl` commands go through it.
- It validates requests and updates etcd.

Try:
```bash
kubectl get --raw /api
```
This shows raw API endpoints exposed by the server.


## Step 3: Explore etcd
The **etcd** database stores cluster state.
- Holds objects like pods, deployments, services.
- Backup/restore of etcd is critical.

Check etcd logs:
```bash
kubectl logs -n kube-system etcd-<node>
```

## Step 4: Explore Scheduler
The **Scheduler** decides which node a pod runs on.
- Factors: resource requests, taints/tolerations, affinity rules.

Test scheduling:
```bash
kubectl create deployment busybox --image=busybox --replicas=2 -- sleep 3600
kubectl get pods -o wide
```
Observe how pods are distributed across worker nodes.

## Step 5: Explore Controller Manager
The **Controller Manager** runs controllers that enforce desired state.
- Examples: ReplicaSet controller, Node controller, Job controller.

Test by scaling a deployment:
```bash
kubectl scale deployment busybox --replicas=5
kubectl get pods
```
The controller manager ensures the pod count matches the desired state.


## Step 6: Visualize Control Plane Architecture

**Control Plane Components:**
- **API Server** → entry point for all requests.
- **etcd** → stores cluster state.
- **Scheduler** → assigns pods to nodes.
- **Controller Manager** → enforces desired state.

Flow:
1. User runs `kubectl apply`.
2. API Server validates and stores object in etcd.
3. Scheduler assigns pod to a node.
4. Controller Manager ensures replicas match desired state.

## Step 7: Cleanup
Delete test deployments:
```bash
kubectl delete deployment busybox
```

## Lab Verification
- You listed control plane pods in `kube-system`.
- You queried API Server endpoints.
- You inspected etcd logs.
- You observed Scheduler distributing pods.
- You saw Controller Manager enforce desired state.


## Next Lab
Proceed to **Lab 7: Worker Node Components (kubelet, kube‑proxy, Container Runtime)**.
 

👉 Shall I prepare **Lab 7 README.md** next, focusing on worker node components (`kubelet`, `kube‑proxy`, container runtime) with verification exercises?
