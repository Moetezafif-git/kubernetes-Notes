# 🌟 Kubernetes Cheatsheet & YAML Examples

![image](https://github.com/user-attachments/assets/f5451a4a-0be5-42be-be5e-41ee4e9ac145)




🚀 This guide covers essential `kubectl` commands and YAML examples for deploying, managing, and scaling Kubernetes resources. Perfect for quick reference! 🌐

---

## 🔧 Commands Overview

Use singular, plural, or short form commands. Here are examples of each:
```bash
kubectl get pod    # Singular
kubectl get pods   # Plural
kubectl get po     # Short form
```

### ⚙️ Create a Pod (Deprecated: `--generator`)
```bash
kubectl run --image=nginx:alpine myfirstpod --labels=example=myfirstpod
```

### YAML Example for Creating a Pod 🐳
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myfirstpod
  labels:
    app: nginx
spec:
  containers:
    - name: nginx
      image: nginx:alpine
      ports:
        - containerPort: 80
```

Use `kubectl get pod -o wide` to get detailed info on a pod and its assigned node. 🖥️

---

## 📝 YAML vs JSON Formats

📄 Display resource output in YAML or JSON:
```bash
kubectl get pod -o yaml
kubectl get pod -o json
```

---

## 🔍 Exploring Kubernetes Resources

Use `kubectl api-resources | grep -i limit` to search for specific resources or `kubectl explain pods --recursive` to deep dive into resource definitions.

---

## 🔄 View and Delete Pods

- Detailed Pod view: 
  ```bash
  kubectl describe pod nginx | less
  ```

- Delete a Pod: 
  ```bash
  kubectl delete pod myfirstpod
  kubectl delete pods --all
  ```

---

## 🔴 Watch Pod Creation in Real-Time

Keep an eye on the Pod status with:
```bash
kubectl get pod -w
```

---

## 🔨 General Command Structure

The typical structure is:
```bash
kubectl <action> <resource_type> <resource_name> <data>
```

📌 **Example:**
```bash
kubectl label pod myfirstpod env=test  # Label a pod
kubectl get pods --show-labels         # Show labels on pods
```

---

## 🧑‍💻 Run Commands Inside Pods

Execute commands inside running containers:
```bash
kubectl exec podname -- env       # Execute in default container
kubectl exec podname -c container_name -- env  # Execute in specific container
```

To open an interactive shell:
```bash
kubectl exec podname -it -- bash   # Single container
kubectl exec podname -c container_name -it -- bash  # Multi-container
```

---

## 🌐 Exposing Pods via Services

Expose your Pod as a service with `kubectl expose`:
```bash
kubectl expose pod podname --port=8000 --target-port=80 --name=myservice
```

### Example YAML for Exposing a Pod 📡
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myservice
spec:
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 8000
      targetPort: 80
```

🔗 Check your services with `kubectl get svc` and use `curl <service-ip>:8000` to interact with the service.

---

## 🔧 Scaling & Replication

🆙 **Scale resources** easily:
```bash
kubectl scale rc myreplicationcontroller --replicas=5
```

### Example YAML for a ReplicationController
```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```

---

## 🚀 Deployments and Rollbacks

Manage rollouts and rollbacks:
```bash
kubectl rollout history deployment mydeployment  # View history
kubectl rollout undo deployment mydeployment     # Rollback to the previous version
kubectl rollout undo deployment mydeployment --to-revision=2  # Rollback to a specific revision
```

### Example YAML for Deployment 📦
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.14.2
          ports:
            - containerPort: 80
```

---

## 📊 Resource Requests and Limits

Set resource requests and limits for containers in your pod spec:
```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

View resource quotas with:
```bash
kubectl describe ns <namespace>
```

### YAML Example for ResourceQuota 📉
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-resources
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi
```

---

## 🔑 ConfigMaps

Create ConfigMaps from various sources:
```bash
kubectl create configmap myconfig --from-literal=special.how=very --from-literal=special.type=charm  # From literals
kubectl create configmap myconfig --from-file=filename                                              # From file
kubectl create configmap myconfig --from-env-file=env.sh                                            # From env file
```

### YAML Example for ConfigMap 📁
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: game-demo
data:
  player_initial_lives: "3"
  ui_properties_file_name: "user-interface.properties"
  game.properties: |
    enemy.types=aliens,monsters
    player.maximum-lives=5
  user-interface.properties: |
    color.good=purple
    color.bad=yellow
    allow.textmode=true
```

---

## 🏷️ Namespaces & Resource Quotas

Create a namespace:
```bash
kubectl create ns myns
```

Apply a resource quota:
```bash
kubectl apply -f quota.yml --namespace=myns
```

### YAML Example for Namespace and Quota
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: myns
---
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: myns
spec:
  hard:
    requests.cpu: "2"
    requests.memory: 2Gi
    limits.cpu: "4"
    limits.memory: 4Gi
```

---

### 👾 Keep Calm and Keep Coding Kubernetes! 👾

This cheatsheet provides core concepts with cool YAML examples. Explore more about Kubernetes resources to master deployments, scaling, and managing workloads seamlessly.

Enjoy the journey! 💻✨
