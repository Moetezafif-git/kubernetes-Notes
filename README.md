# 🌟 Kubernetes Cheatsheet & YAML Examples

![image](https://github.com/user-attachments/assets/f5451a4a-0be5-42be-be5e-41ee4e9ac145)






- You can use singular, plural, or abbreviated forms of commands with `kubectl`:
  ```
  kubectl get pod
  kubectl get pods
  kubectl get po
  ```

- To create your first pod using a generator (note that the generator option is deprecated):
  ```bash
  kubectl run --image=nginx:alpine myfirstpod --labels=example=myfirstpod
  ```
  (The previous generator command will not work anymore.)

- To get detailed information about the pods running on nodes:
  ```bash
  kubectl get po -o wide
  ```

- For YAML and JSON format output:
  ```bash
  kubectl get po -o yaml
  kubectl get po -o json
  ```

- If you want to search for a specific resource:
  ```bash
  kubectl api-resources | grep -i limit
  ```

- To learn more about a specific resource, such as pods:
  ```bash
  kubectl explain pods | less
  kubectl explain pod --recursive | less
  ```

- To view details about a specific pod:
  ```bash
  kubectl describe pod nginx | less
  ```

- To delete a pod (e.g., if it's in a crash loop):
  ```bash
  kubectl delete pod myfirstpod
  kubectl delete pods --all
  kubectl edit pod podname
  ```

- To watch pod creation in real-time:
  ```bash
  kubectl get pod -w
  ```

- General command structure:
  ```
  kubectl <action> <resource_type> <resource_name> <data>
  ```
  Example:
  ```bash
  kubectl label pod myfirstpod env1=lool
  ```
  To label all pods:
  ```bash
  kubectl label pod --all env1=lool
  ```

- To view pods along with their labels:
  ```bash
  kubectl get pods --show-labels
  ```

- To run a command inside a pod's container:
  ```bash
  kubectl exec podname env
  ```
  If there are multiple containers in the pod:
  ```bash
  kubectl exec podname -c container_name
  ```

- To access the bash shell of a container:
  ```bash
  kubectl exec podname -it bash
  ```
  If there are multiple containers:
  ```bash
  kubectl exec podname -c container_name -it bash
  ```

- To set a command inside a container:
  ```yaml
  args: ['sleep', '50']
  ```

- To grep for the name in a `pod1.yml` file:
  ```bash
  grep name podname.yml
  ```

- To make a container listen on a specific port:
  ```bash
  netcat -l -p 8000
  ```
  To see which ports are open:
  ```bash
  netstat -nltp
  ```

- If you have three pods and assign a ClusterIP, they can communicate with each other. For external communication, you will need a NodePort. The following image illustrates this concept:

![image](https://github.com/user-attachments/assets/53f3d535-2fbd-4714-bfd4-aeb9b2e6867f)
![image](https://github.com/user-attachments/assets/3c14ff61-d26a-450a-a385-4f1d6ae76788)

- To create a service for communication between pods:
  ```bash
  kubectl expose pod podname --port=8000 --target-port=80 --name myservice
  ```
  You can check this using:
  ```bash
  kubectl get svc/service/services
  ```
  Then test the service with:
  ```bash
  curl ip:8000
  ```

- To assign a NodePort for external access:
  ```bash
  kubectl expose pod podname --type=NodePort --port=8000 --target-port=80 --name myservice  # Note: may not work in Minikube
  ```

- **How Services Work**: In a service YAML file, the selector is crucial. If any pod has the same label as the service selector, the service will distribute requests among these pods. You can add a label to a pod like this:
  ```bash
  kubectl label pod myfirstpod env1=lool
  ```



- Running a Replication Controller (RC) will create replicas based on the template. If a pod with the same label already exists, it won’t create a new pod; however, if an existing pod is owned by another RC, a new pod will be created according to the new RC.

  If you don’t specify a selector, it defaults to using the template label:
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
        name: nginx
        labels:
          app: nginx
      spec:
        containers:
        - name: nginx
          image: nginx
          ports:
          - containerPort: 80
  ```

  If you delete a pod's label (e.g., `app=nginx`), it will automatically recreate the pod. If you delete the RC, all its replicas will terminate, but using `--cascade=false` will only delete the RC without terminating its pods:
  ```bash
  kubectl delete rc nginx --cascade=false
  ```

- Scaling up or down is straightforward. You can set the number of replicas with:
  ```bash
  kubectl scale rc --replicas=5 replicaControllerName
  ```
  Alternatively, you can edit the RC directly:
  ```bash
  kubectl edit rc name
  ```

- ReplicaSets share many characteristics with Replication Controllers but offer additional features, such as label matching expressions (e.g., `In`, `NotIn`).

![image](https://github.com/user-attachments/assets/33b71e15-1ed5-4b7f-839d-67cff8b58f1b)

- **Deployments** are similar to ReplicaSets with some enhancements:
  - Rollout new versions.
  - Rollback to previous versions.
  - Recreate policy: sets the old version's replicas to 0 while creating replicas for the new version.
  - Adjust replica count as needed.

  Rollout triggers only on spec changes (i.e., a new version). The `maxUnavailable` setting specifies how many pods can be missing during an update, while `maxSurge` determines how many extra pods can be created.

  To view rollout history:
  ```bash
  kubectl rollout history deployment name_of_the_deployment
  ```
  To undo a deployment:
  ```bash
  kubectl rollout undo deployment name_of_the_deployment
  ```
  To revert to a specific revision:
  ```bash
  kubectl rollout undo --to-revision=2 deployment name_of_the_deployment
  ```
  To pause and resume a deployment:
  ```bash
  kubectl rollout pause deployment name_of_the_deployment
  kubectl rollout resume deployment name_of_the_deployment
  ```

  You can apply a deployment from a YAML file with:
  ```bash
  kubectl apply -f firstdeploy.yml --record
  ```

  Example Deployment YAML:
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

- **Resource Limits**: To check memory usage for a container:
  ```bash
  docker container stats container_id
  ```
  To set fixed memory and CPU limits, refer to the image below during pod creation:

  ![Resource Limits](./reqlimit.png)

- By default, all resources are created in the default namespace, but there is also a public namespace that doesn’t require authentication for access. You can see many pods that control master system management with:
  ```bash
  kubectl get pods -n kube-system
  ```

  To change the default namespace:
  ```bash
  kubectl config set-context --current --namespace=test
  ```

- To check if a resource is supported in a namespace:
  ```bash
  kubectl api-resources | grep -i pod
  ```
  To apply a pod in a specific namespace:
  ```bash
  kubectl apply -f pod_yml --namespace test
  ```
  To view pods in the `test` namespace:
  ```bash
  kubectl get pods -n test
  kubectl get pods --all-namespaces  # To see all namespaces' pods
  ```

- To delete a pod in a specific namespace:
  ```bash
  kubectl delete pod podname -n test
  ```

- To create a namespace:
  ```bash
  kubectl create ns myns
  ```

  To connect with a service in another namespace:
  ```bash
  curl servicename.namespacename.svc.cluster.local
  ```

- **Resource Quota**: This defines limits/specifications for resources within namespaces. There are two types of quotas:
  - Compute quota (e.g., `request.cpu`)
  - Object quota (e.g., limits on thecontinuation of Kubernetes Notes:

---

number of pods, services, etc.)

- **ConfigMap and Secret**: These are used for storing configuration data and sensitive information, respectively. Here’s how to create and use them:

  - **Creating a ConfigMap**:
    ```bash
    kubectl create configmap my-config --from-literal=key1=value1 --from-literal=key2=value2
    ```

  - **Creating a Secret**:
    ```bash
    kubectl create secret generic my-secret --from-literal=password=mysecretpassword
    ```

  - **Using ConfigMap in a Pod**:
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: mypod
    spec:
      containers:
      - name: mycontainer
        image: myimage
        env:
        - name: MY_CONFIG
          valueFrom:
            configMapKeyRef:
              name: my-config
              key: key1
    ```

  - **Using Secret in a Pod**:
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: mypod
    spec:
      containers:
      - name: mycontainer
        image: myimage
        env:
        - name: MY_SECRET
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: password
    ```

- **Volume**: Volumes provide a way to share data between containers in a pod. Types of volumes include `emptyDir`, `hostPath`, `persistentVolumeClaim`, and more.

  - **Example of a Volume in a Pod**:
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: mypod
    spec:
      containers:
      - name: mycontainer
        image: myimage
        volumeMounts:
        - mountPath: /data
          name: my-volume
      volumes:
      - name: my-volume
        emptyDir: {}
    ```

- **Persistent Volumes (PV) and Persistent Volume Claims (PVC)**:
  - **Creating a Persistent Volume**:
    ```yaml
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: my-pv
    spec:
      capacity:
        storage: 1Gi
      accessModes:
        - ReadWriteOnce
      hostPath:
        path: /mnt/data
    ```

  - **Creating a Persistent Volume Claim**:
    ```yaml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: my-pvc
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 1Gi
    ```

  - **Using PVC in a Pod**:
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: mypod
    spec:
      containers:
      - name: mycontainer
        image: myimage
        volumeMounts:
        - mountPath: /data
          name: my-pv-volume
      volumes:
      - name: my-pv-volume
        persistentVolumeClaim:
          claimName: my-pvc
    ```

- **Network Policies**: Define how pods communicate with each other and with other network endpoints. For instance, to restrict access to a specific pod:
  ```yaml
  apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
    name: my-network-policy
  spec:
    podSelector:
      matchLabels:
        app: myapp
    policyTypes:
    - Ingress
    ingress:
    - from:
      - podSelector:
          matchLabels:
            role: frontend
  ```

- **Ingress**: A collection of rules for the inbound traffic to reach the services. It provides HTTP and HTTPS routing to services based on the request URL.
  
  - **Example Ingress Resource**:
    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: my-ingress
    spec:
      rules:
      - host: myapp.example.com
        http:
          paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-service
                port:
                  number: 80
    ```

- **Helm**: A package manager for Kubernetes, used to manage Kubernetes applications. Helm uses **charts**, which are packages of pre-configured Kubernetes resources.

  - **Installing Helm**:
    ```bash
    curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
    ```

  - **Creating a Helm Chart**:
    ```bash
    helm create mychart
    ```

  - **Installing a Helm Chart**:
    ```bash
    helm install myrelease mychart
    ```

- **Monitoring and Logging**: Tools like Prometheus for monitoring and Grafana for visualization, along with ELK (Elasticsearch, Logstash, Kibana) stack for logging, are commonly used in Kubernetes environments.

  - **Installing Prometheus**:
    ```bash
    kubectl apply -f prometheus.yml
    ```

  - **Installing Grafana**:
    ```bash
    kubectl apply -f grafana.yml
    ```

---
