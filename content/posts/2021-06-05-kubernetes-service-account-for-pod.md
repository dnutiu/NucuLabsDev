---
author: "Denis Nu\u021Biu"
categories:
- Cloud
- Linux
- Tutorial
date: '2021-06-05T13:22:42+00:00'
guid: https://nuculabs.dev/?p=1403
id: 1403
image: /wp-content/uploads/2021/05/illustration.jpg
layout: post
permalink: /2021/06/05/kubernetes-service-account-for-pod/
tags:
- authentication
- authorization
- kubernetes
- rbac
- service account
title: Kubernetes service account for pod
---
Hi 🙋‍♂️,


In this article I will talk about how to authenticate your applications to the Kubernetes API via the service accounts feature.


Citing the Kubernetes [docs](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/), a service account for a pod: “provides an identity for processes that run in a Pod.


When you (a human) access the cluster (for example, using `kubectl`), you are authenticated by the apiserver as a particular User Account (currently this is usually `admin`, unless your cluster administrator has customized your cluster). Processes in containers inside pods can also contact the apiserver. When they do, they are authenticated as a particular Service Account (for example, `default`).” 💡


![](/wp-content/uploads/2021/05/illustration.jpg?w=800)
## Creating a service account


Let’s start [Minikube](https://minikube.sigs.k8s.io/docs/start/)


```
PS C:\Users\denis> minikube start
😄  minikube v1.20.0 on Microsoft Windows 10 Home 10.0.19042 Build 19042
✨  Using the docker driver based on existing profile
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
🔄  Restarting existing docker container for "minikube" ...
🐳  Preparing Kubernetes v1.20.2 on Docker 20.10.6 ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```


After Minikube has started, we can check what service accounts we currently have by typing:


```
PS C:\Users\denis> kubectl get serviceaccounts
NAME      SECRETS   AGE
default   1         8d
```


🤖 To create a new service account, we create a new file sa.yaml and apply it with `kubectl apply -f .\sa.yaml`


```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: ubuntu-user
```


If you want to add permissions to the service account, you can use various [authorization modules](https://kubernetes.io/docs/reference/access-authn-authz/authorization/). I will grant **ubuntu-user** permissions for all APIs using the following ClusterRole and ClusterRoleBinding 🔐


```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: all-apis
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["*"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: all-apis-binding
  namespace: default
subjects:
- kind: ServiceAccount
  name: ubuntu-user
  namespace: default
roleRef:
  kind: Role
  name: all-apis
  apiGroup: rbac.authorization.k8s.io
```


## Ubuntu Instance


For demonstration purposes I’m going to create a new deployment based on an Ubuntu image and ssh into it. 🐬


Create a new file deployment.yaml and paste the following contents, then create the deployment with `kubectl apply -f deployment.yaml`


```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ubuntu-dev
  labels:
    app: ubuntu-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ubuntu-demo
  template:
    metadata:
      labels:
        app: ubuntu-demo
    spec:
      serviceAccountName: ubuntu-user
      containers:
      - name: ubuntu-demo
        image: ubuntu:20.04
        command: ["/bin/sleep", "3650d"]
```


It will create an Ubuntu instance inside Kubernetes. To get a shell, run the following commands. *Don’t forget to replace the pod name with your own.*


```
PS kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
ubuntu-dev-68f4b48dd-xlcqp   1/1     Running   0          78s
PS kubectl exec --stdin --tty ubuntu-dev-68f4b48dd-xlcqp -- /bin/bash
root@ubuntu-dev-68f4b48dd-xlcqp:/#
```


🔧To install `kubectl` we need to run the following commands:


```
apt-get update && apt-get -y install curl gnupg2
touch /etc/apt/sources.list.d/kubernetes.list && echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | tee -a /etc/apt/sources.list.d/kubernetes.list
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
apt-get update && apt-get install -y kubectl
```


After the commands have finished running, we can execute any `kubectl` command from inside our pod and we didn’t have to worry about **configuration files** or authenticating our pod. For example:


```
root@ubuntu-dev-68f4b48dd-fkhht:/# kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.245.0.1   
        443/TCP   2d8h
```


This works because Kubernetes injects some environment variables into the pod and auto mounts some secrets as well:


```
root@ubuntu-dev-68f4b48dd-fkhht:/# env | grep KUBE
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_SERVICE_PORT=443
KUBERNETES_PORT_443_TCP=tcp://10.245.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_ADDR=10.245.0.1
KUBERNETES_SERVICE_HOST=10.245.0.1
KUBERNETES_PORT=tcp://10.245.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443


root@ubuntu-dev-68f4b48dd-fkhht:/# ls /var/run/secrets/kubernetes.io/serviceaccount/
ca.crt  namespace  token
```


This mechanism allows `kubectl` and various **client libraries** to pick up configuration automatically. ✨


🧼To reset our work, we can run:


```
kubectl delete -f .\deployment.yaml
kubectl delete -f .\permissions.yaml
kubectl delete -f .\sa.yaml
```


Thank you for reading! 📖


### References


- 
- 