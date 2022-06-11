# Kubernetes Basics ⛵

In docker we faced a problem called container orchestration so for solution of that there was docker swarm but another solution offered by google is **Kubernetes**

Now to set up we can use GCP, Azure or **LINODE**
We can configure our Kubernetes using Linode cloud provider →

[https://cloud.linode.com/kubernetes/create](https://cloud.linode.com/kubernetes/create)

Now fill the details and create a node

![Screenshot 2022-06-11 at 3.38.42 PM.png](Kubernetes%201627a4705afa41d2af0657bed77d99bd/Screenshot_2022-06-11_at_3.38.42_PM.png)

Now after creating we will get a master node link and three nodes which we selected

![Screenshot 2022-06-11 at 3.38.42 PM.png](Kubernetes%201627a4705afa41d2af0657bed77d99bd/Screenshot_2022-06-11_at_3.38.42_PM%201.png)

Now we need to install kubectl on our host machine which is required for using kubernetes.

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
```

→ 

```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 43.5M  100 43.5M    0     0  7513k      0  0:00:05  0:00:05 --:--:-- 8750k
```

Now in the directory we have our kubectl and we need to configure it now →

```bash
chmod +x kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

Now copy our kubernetes yaml config file

![Screenshot 2022-06-11 at 5.45.43 PM.png](Kubernetes%201627a4705afa41d2af0657bed77d99bd/Screenshot_2022-06-11_at_5.45.43_PM.png)

```bash
nano kubeconfig.yaml
```

And here paste it and save the file

→ Export the file name as a variable

```bash
export KUBECONFIG=kubeconfig.yaml
```

## For MacOS

```bash
brew install kubectl
# This will install and link the kubectl to CLI
```

→ Now continuing

### To get the nodes on the cluster (for this to work we need to specify our kubeconfig.yaml file )

```bash
kubectl get nodes
```

→ Get nodes

```bash
NAME                          STATUS   ROLES    AGE    VERSION
lke63625-98873-62a469e5f73b   Ready    <none>   133m   v1.23.6
lke63625-98873-62a469e6c87d   Ready    <none>   133m   v1.23.6
lke63625-98873-62a469e795e1   Ready    <none>   133m   v1.23.6
```

### To get info about the cluster

```bash
kubectl cluster-info
```

→

```bash
Kubernetes control plane is running at https://c9abbb71-4d6e-4e08-b874-e99614d81203.ap-west-2.linodelke.net:443
KubeDNS is running at https://c9abbb71-4d6e-4e08-b874-e99614d81203.ap-west-2.linodelke.net:443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

### To run our image

```bash
kubectl run <pesonalized_name> --image=<image_path> --port=<port_you_want_to_run_container_on>
```

This will run our application in a pod 

### To get pods running

```bash
kubectl get pods
```

### To get the more info about the running pods

```bash
kubectl describe pods
```

## Deployment

Now if we want to deploy our machine to the worker nodes and we want to specify what we need in each node then we need to do it by defining in a yaml file

For eg:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: networkchuckcoffee-deployment
  labels:
    app: nccoffee
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nccoffee
  template:
    metadata:
      labels:
        app: nccoffee
    spec:
      containers:
      - name: nccoffee
        image: thenetworkchuck/nccoffee:pourover
        imagePullPolicy: Always
        ports:
        - containerPort: 80
```

To deploy using the config defined by us in this yaml file we will use this command

```bash
kubectl apply -f <yaml_filename>
```

### Delete a pod

```bash
kubectl delete pods <podname>
```

### Get deployments

```bash
kubectl get deployments
```

### Edit deployment

Now in kubernetes it always keeps checking that how our deployment is performing like if I specified i need 3 replicas of my application then it keeps ensuring that I need 3 instances of our application running all the time

We can also edit our deployment by editing the file

```bash
kubectl get deployments
kubectl edit deployment <deployment_name>
```

### Getting more details or pods

```bash
kubectl get pods -o wide
```

## Service

Now if we need to make our website accessible to whole over the internet then we need to expose our container and for that we will user service and it will also act as an load balancer

service example

```yaml
apiVersion: v1
kind: Service
metadata:
  name: coffee-service
  annotations:
    service.beta.kubernetes.io/linode-loadbalancer-throttle: "4"
  labels:
    app: coffee-service
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nccoffee
  sessionAffinity: None
```

→

```bash
nano  coffee-service.yaml
kubectl apply -f coffee-service.yaml
```

### Get running services

```bash
kubectl get services
```

For more details about a service

```bash
kubectl describe services <service_name>
```
