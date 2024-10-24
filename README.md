# 🚀 Kubernetes Mastery Guide

![image](https://github.com/user-attachments/assets/f5451a4a-0be5-42be-be5e-41ee4e9ac145)



- You can use singular, plural, or short form commands, for example:
  ```bash
  kubectl get pod
  kubectl get pods
  kubectl get po
  ```

## Creating a Pod with Generator
> **Note:** The `--generator` flag is deprecated.
```bash
kubectl run --image=nginx:alpine myfirstpod --labels=example=myfirstpod
```

### Example YAML for Pod
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

- Get details of a pod running on a node:
  ```bash
  kubectl get po -o wide
  ```

## YAML and JSON Formats
- Get resource output in YAML or JSON:
  ```bash
  kubectl get po -o yaml
  kubectl get po -o json
  ```

## Understanding Kubernetes Resources
- To find a resource:
  ```bash
  kubectl api-resources | grep -i limit
  ```
- To learn about a resource:
  ```bash
  kubectl explain pods | less
  kubectl explain pod --recursive | less
  ```

## Viewing and Deleting Pods
- View detailed pod information:
  ```bash
  kubectl describe pod nginx | less
  ```
- Delete a pod:
  ```bash
  kubectl delete pod myfirstpod
  kubectl delete pods --all
  kubectl edit pod podname
  ```

## Watching Pods Creation
- Watch a pod being created:
  ```bash
  kubectl get pod -w
  ```

## General Command Structure
```bash
kubectl <action> <resource_type> <resource_name> <data>
```
- Example: Adding a label to a pod:
  ```bash
  kubectl label pod myfirstpod env1=test
  ```
- Label all pods:
  ```bash
  kubectl label pod --all env1=test
  ```

- Get pods with labels:
  ```bash
  kubectl get pods --show-labels
  ```

## Running Commands Inside a Pod
- Execute a command inside a pod:
  ```bash
  kubectl exec podname -- env
  ```
- If the pod has multiple containers:
  ```bash
  kubectl exec podname -c container_name -- env
  ```

- Enter a container’s bash shell:
  ```bash
  kubectl exec podname -it -- bash
  ```
- For multiple containers:
  ```bash
  kubectl exec podname -c container_name -it -- bash
  ```

## Setting Commands in a Container
- Set commands for a container in the pod YAML:
```yaml
args: ['sleep', '50']
```

## Grep in Pod YAML
```bash
grep name podname.yml
```

## Exposing Pods via Services
- To expose a pod:
  ```bash
  kubectl expose pod podname --port=8000 --target-port=80 --name=myservice
  ```

### Example YAML for a Service
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

- To list services and get the IP and port:
  ```bash
  kubectl get svc
  curl <service-ip>:8000
  ```

- Exposing a pod with NodePort for external access:
  ```bash
  kubectl expose pod podname --type=NodePort --port=8000 --target-port=80 --name=myservice
  ```

## Scaling and Replication
- Scaling up/down the number of replicas:
  ```bash
  kubectl scale rc --replicas=5 replicaControllerName
  ```
- Edit the replication controller to scale:
  ```bash
  kubectl edit rc rcname
  ```

### Example YAML for a Replication Controller
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

- Deleting an RC without deleting its pods:
  ```bash
  kubectl delete rc rcname --cascade=false
  ```

## Deployments and Rollouts
- Rollout and rollback deployments:
  ```bash
  kubectl rollout history deployment mydeployment
  kubectl rollout undo deployment mydeployment
  kubectl rollout undo deployment mydeployment --to-revision=2
  ```

### Example YAML for Deployment
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
