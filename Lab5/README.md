# Lab 5: Writing and Validating YAML Manifests for Kubernetes

## Objectives
- Learn YAML basics and syntax rules.
- Write a simple Pod manifest in YAML.
- Validate YAML manifests before applying.
- Apply manifests to the cluster and verify resources.
- Debug common YAML issues.


## Step 1: Understand YAML Basics
- YAML = “YAML Ain’t Markup Language” → human‑friendly data format.
- Key rules:
  - Indentation matters (use spaces, not tabs).
  - Key‑value pairs: `key: value`.
  - Lists: `- item`.
  - Strings don’t need quotes unless special characters are used.

Example:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-config
data:
  key1: value1
  key2: value2
```

## Step 2: Write a Simple Pod Manifest
Create a file `nginx-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      ports:
        - containerPort: 80
```

## Step 3: Validate YAML Syntax
Use `kubectl` dry‑run mode:
```bash
kubectl apply --dry-run=client -f nginx-pod.yaml
```
- Validates syntax locally without creating resources.

Check schema:
```bash
kubectl explain pod
```
- Shows fields and documentation for Pod objects.


## Step 4: Apply the Manifest
Create the pod:
```bash
kubectl apply -f nginx-pod.yaml
```

Verify:
```bash
kubectl get pods
kubectl describe pod nginx-pod
```


## Step 5: Debug Common YAML Issues
- **Indentation errors** → always use 2 spaces per level.
- **Missing apiVersion/kind** → required fields.
- **Wrong field names** → check with `kubectl explain`.
- **Image pull errors** → check with `kubectl describe pod`.


## Step 6: Clean Up
Delete the pod when finished:
```bash
kubectl delete -f nginx-pod.yaml
```


## Lab Verification
- You wrote a valid YAML manifest.
- You validated it with `--dry-run`.
- You deployed and verified the pod.
- You learned how to debug YAML issues.


## Next Lab
Proceed to **Lab 6: Understanding Control Plane Components (API Server, etcd, Scheduler, Controller Manager)**.

This README gives you a **hands‑on YAML lab** with writing, validation, deployment, and debugging.  

👉 Shall I prepare **Lab 6 README.md** next, explaining control plane components with diagrams and commands to inspect them?
