---
author: ["Denis Nu\u021Biu"]
categories:
- Programming
- Tutorial
- Cloud
date: '2023-04-13T20:43:49+00:00'
layout: post
permalink: /2023/04/13/working-with-volumes-in-microk8s/
tags:
- kubernetes
- tutorial
- microk8s
title: "Working with Volumes in Microk8s"
---

# Introduction

Hello 👋,

In this quick tutorial 💻 we’ll explore how to use Volumes and PersistentVolumes with hostpath storage in Microk8s.

We will deploy a simple nginx instance and mount a volume inside it that points to the **~/Downloads** folder.

Let’s get started!

Note: The following image was generated with Stable Diffusion

![hero](/hugo-content/2023-05/hero1.webp)

# How-To

You can follow along if you want to setup a quick deployment with a host path volume.

## Persistent Volume

Let’s start by creating a Persistent Volume resource:

```bash
k apply -f persistent-volume.yaml
```

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nuculabs-persistent-volume
  labels:
    app: nginx
    owner: dnutiu
    name: tutorial-volume
spec:
  storageClassName: microk8s-hostpath
  # Exclusive to PVC
  claimRef:
    name: nuculabs-persistent-volume-claim
    namespace: default
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/home/denis/Downloads"
```

After applying the file you should see:

```bash
➜  Downloads k apply -f persistent_volume.yml 
persistentvolume/nuculabs-persistent-volume created
➜  Downloads k get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                                      STORAGECLASS        REASON   AGE
nuculabs-persistent-volume                 1Gi        RWX            Retain           Available   default/nuculabs-persistent-volume-claim   microk8s-hostpath            5s
```

The created persistent volume is exclusive to the PersistentVolumeClaim we’ll create next.

## Persistent Volume Claim

To reserve the **nuculabs-persistent-volume** Persistent Volume we need to create a claim:

```bash
k apply -f persistent-volume-claim.yaml
```

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nuculabs-persistent-volume-claim
  namespace: default
  labels:
    app: nginx
    owner: dnutiu
    name: tutorial-volume
spec:
  storageClassName: "microk8s-hostpath"
  volumeName: nuculabs-persistent-volume
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 500Gi
```

We can ensure that the claim is bound to our PV by referencing the **volumeName**:

```yaml
spec:
  storageClassName: "microk8s-hostpath"
  volumeName: nuculabs-persistent-volume
```

or by using the **matchLabels** selector:

```yaml
spec:
  storageClassName: "microk8s-hostpath"
  selector:
    matchLabels:
      app: nginx
      owner: dnutiu
      name: tutorial-volume
```

To verify that the volume is bound run:

```bash
k get pv

NAME  STATUS   CLAIM  
nuculabs-persistent-volume ...        Bound    default/nuculabs-persistent-volume-claim 
```

It should list the volume name along with the Bound status.

If you’re following along, congrats for making it this far! 😁

![hero](/hugo-content/2023-05/hero2.webp)

## Deployment

Finally, let’s create our nginx deployment that contains the mounted volume.

```bash
k apply -f nginx-deployment.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 1
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
        volumeMounts:
          - mountPath: "/data"
            name: data-volume
      volumes:
        - name: "data-volume"
          persistentVolumeClaim:
            claimName: nuculabs-persistent-volume-claim
```

We mount the volume to the nginx container with:

```yaml
        volumeMounts:
          - mountPath: "/data"
            name: data-volume
```

Then we add volumes to the spec and map “data-volume” to our PVC:

```yaml
      volumes:
        - name: "data-volume"
          persistentVolumeClaim:
            claimName: nuculabs-persistent-volume-claim
```

That’s it! 🎉

If you want to verify that the volume is mounted correctly you can SSH into the nginx pod and navigate to the /data directory.

Thanks for reading! 📚