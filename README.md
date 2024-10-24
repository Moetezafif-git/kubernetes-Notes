# 🚀 Kubernetes Mastery Guide

![image](https://github.com/user-attachments/assets/f5451a4a-0be5-42be-be5e-41ee4e9ac145)


Here’s a refined Markdown file with YAML examples illustrating Kubernetes concepts, commands, and workflows:

# Kubernetes Notes

![Kubernetes Architecture](./kubernetes_architect.drawio.png)

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
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

## Resource Requests and Limits
- Define resource limits and requests in the pod spec:
```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

- View resource quota for a namespace:
  ```bash
  kubectl describe ns myns
  ```

### Example YAML for Resource Quota
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

## ConfigMaps
- Create a ConfigMap from literals:
  ```bash
  kubectl create configmap special-config --from-literal=special.how=very --from-literal=special.type=charm
  ```

- Create a ConfigMap from files:
  ```bash
  kubectl create configmap cm_name --from-file=filename
  kubectl create configmap cm_name --from-file=properties/
  ```

- Create a ConfigMap from an environment file:
  ```bash
  kubectl create configmap cm_name --from-env-file=env.sh
  ```

### Example YAML for ConfigMap
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

## Namespace and Quotas
- Create a namespace:
  ```bash
  kubectl create ns myns
  ```

- Apply resource quotas:
  ```bash
  kubectl apply -f quota.yml --namespace=myns
  ```

### Example YAML for Namespace and Quota
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

This version includes clear examples of YAML for various Kubernetes components and provides practical commands for managing resources.
