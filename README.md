# 🚀 Kubernetes Mastery Guide

![image](https://github.com/user-attachments/assets/f5451a4a-0be5-42be-be5e-41ee4e9ac145)


### 🌐 **Kubernetes Command Flexibility**

Kubernetes allows flexible ways to interact with resources:

```bash
kubectl get pod            # Singular form
kubectl get pods           # Plural form
kubectl get po             # Short form
```

---

### 🛠 **Create Your First Pod Like a Pro!**

> **Deprecated Approach** (Avoid using this in modern Kubernetes versions):
```bash
kubectl run pod --generator=run-pod/v1 --image=coolgourav/nginx-custom
```

> **Modern Approach**:
```bash
kubectl run myfirstpod --image=nginx:alpine --labels=example=myfirstpod
```

Check the pod details (including which node it's running on):
```bash
kubectl get po -o wide
```

---

### 🔍 **Viewing Resources in Style**

View resources in **YAML** or **JSON** format:
```bash
kubectl get po -o yaml
kubectl get po -o json
```

---

### 📖 **Exploring Resources - Knowledge is Power!**

Don’t know what a resource is? Let Kubernetes explain it to you:
```bash
kubectl explain pods | less          # Learn about pods
kubectl explain pod --recursive | less
```

---

### 🔎 **Inspecting Pods Like a Detective**

Want to dive into the specifics of your pod? Run:
```bash
kubectl describe pod nginx | less
```

---

### 🚫 **Managing Pods: Creation and Deletion**

To delete pods (even those stuck in a `CrashLoopBackOff` state):
```bash
kubectl delete pod myfirstpod
kubectl delete pods --all           # Deletes all pods
```

---

### 👀 **Real-Time Pod Creation - Watch the Magic!**

Observe your pod being created in real time:
```bash
kubectl get pod -w
```

---

### 🏷 **Labeling: Tag Your Pods for Easy Identification**

Add labels to your pods like a pro:
```bash
kubectl label pod myfirstpod env1=lool
kubectl label pod --all env1=lool    # Label all pods
```

View your labels:
```bash
kubectl get pods --show-labels
```

---

### 🖥 **Running Commands Inside Pods: Shells, Variables & More!**

Run environment commands inside a pod:
```bash
kubectl exec podname env
```

Get inside the container’s shell:
```bash
kubectl exec podname -it bash
```

For multi-container pods, specify the container:
```bash
kubectl exec podname -c container_name -it bash
```

---

### 🌐 **Networking Insights**

Listen on a specific port:
```bash
netcat -l -p 8000
```

Check open ports:
```bash
netstat -nltp
```

---

### 🔧 **Services: Expose Your Pods Like a Pro**

Use a **ClusterIP** for internal communication, or expose it externally with a **NodePort**:
```bash
kubectl expose pod podname --port=8000 --target-port=80 --name=myservice
kubectl expose pod podname --type=NodePort --port=8000 --target-port=80 --name=myservice
```

Check the service:
```bash
kubectl get svc
```

Test it with `curl`:
```bash
curl <NodeIP>:<NodePort>
```

![Service Types](./service.png)

---

### 🎛 **Deployments: Scaling and Rolling Updates**

Deployments are your powerhouse for managing rolling updates, rollbacks, and scaling:
```bash
kubectl rollout history deployment <deployment_name>    # Check deployment history
kubectl rollout undo deployment <deployment_name>       # Undo the last deployment
kubectl rollout undo --to-revision=2 deployment <deployment_name>   # Roll back to a specific revision
```

You can **pause** or **resume** deployments:
```bash
kubectl rollout pause deployment <deployment_name>
kubectl rollout resume deployment <deployment_name>
```

Or create them via YAML:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
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

### 📊 **Resource Quotas: Control Resource Usage Like a Boss**

Set limits on resources in namespaces with resource quotas:
```bash
kubectl create ns myns
kubectl get resourcequotas -n myns
kubectl apply -f quota.yml --namespace=myns
```

**Example Quota YAML**:
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: myns
spec:
  hard:
    requests.cpu: "4"
    requests.memory: 1Gi
    limits.cpu: "10"
    limits.memory: 2Gi
```

---

### 🏁 **The Final Frontier: Kubernetes Mastery**

Kubernetes is a beast, but with these commands, you're in control. Whether it's creating pods, scaling deployments, or managing resource quotas, you've got the tools to master your Kubernetes cluster!

![Replication Set](./rcset.png)

---
