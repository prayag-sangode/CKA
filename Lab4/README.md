# Lab 4: Managing Clusters Using kubectl and kubeconfig

## Objectives
- Understand the purpose of the **kubeconfig** file.
- Learn how to view and switch between cluster contexts.
- Create custom contexts with namespaces.
- Inspect kubeconfig YAML structure.
- Practice deploying workloads across different contexts.


## Step 1: Understand kubeconfig
- Default location: `~/.kube/config`
- Stores:
  - **clusters** → API server endpoints & certificates
  - **users** → authentication info
  - **contexts** → mapping of cluster + user + namespace

Check the file:
```bash
cat ~/.kube/config
```

## Step 2: View Current Context
```bash
kubectl config current-context
kubectl config get-contexts
```
- Shows all available contexts.
- Active one is marked with `*`.

## Step 3: Switch Between Contexts
If you have multiple clusters (e.g., `k8s-node1`, `k8s-node2`):
```bash
kubectl config use-context k8s-node1
kubectl config use-context k8s-node2
```

## Step 4: Create a New Context
```bash
kubectl config set-context lab-context \
  --cluster=kubernetes \
  --user=kubernetes-admin \
  --namespace=default
```

Switch to it:
```bash
kubectl config use-context lab-context
```

## Step 5: Use Namespaces with Contexts
Set a default namespace for a context:
```bash
kubectl config set-context dev-context \
  --cluster=kubernetes \
  --user=kubernetes-admin \
  --namespace=development
```

Switch:
```bash
kubectl config use-context dev-context
```

Now all kubectl commands default to the `development` namespace.


## Step 6: Inspect kubeconfig File
Open the file:
```bash
cat ~/.kube/config
```
You’ll see YAML with clusters, users, and contexts defined.


## Step 7: Hands‑On Verification
Deploy a pod in different contexts:
```bash
kubectl run testpod --image=nginx
kubectl get pods
```

Switch context and deploy again:
```bash
kubectl config use-context lab-context
kubectl run testpod2 --image=nginx
kubectl get pods
```

Confirms workloads are isolated per cluster/context.


## Lab Verification
- You listed and switched contexts.
- You created custom contexts with namespaces.
- You inspected kubeconfig YAML.
- You deployed pods across different contexts.


## Next Lab
Proceed to **Lab 5: Writing and Validating YAML Manifests for Kubernetes**.
