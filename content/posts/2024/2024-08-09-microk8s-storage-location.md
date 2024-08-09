---
title: "Changing Microk8s' default hostpath addon storage location"
author: [ "Denis Nutiu" ]
date: '2024-08-09'
categories:
  - "Cloud"
  - "Tutorial"
permalink: /2024/08/08/microk8s-storage-location
tags:
  - kubernetes
  - microk8s
  - canonical
  - storage
  - hostpath
  - how to change path
layout: post
published: true
---

Hello everyone,

This is a short guide on how to change Microk8s' default storage path for the hostpath addon.

First, ensure the addon is enabled:

```shell
microk8s enable hostpath-storage
```

Then create a new directory in which you want to store volumes created in the Microk8s instance.

```shell
mkdir -p /var/microk8s-volumes
```

Then create and apply the storage class yaml file:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: custom-storageclass
provisioner: microk8s.io/hostpath
reclaimPolicy: Retain
parameters:
  pvDir: /var/microk8s-volumes
volumeBindingMode: WaitForFirstConsumer
```

To change the default storage class for Microk8s you need to run these commands:

```shell
kubectl patch storageclass microk8s-hostpath -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'
kubectl patch storageclass custom-storageclass -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```