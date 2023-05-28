---
author: "Denis Nu\u021Biu"
categories:
- Cloud
- Tutorial
date: '2023-03-29T20:30:00+00:00'
guid: https://nuculabs.dev/?p=2684
id: 2684
layout: post
permalink: /2023/03/29/how-to-create-a-servicemonitor-in-microk8s-observability-tutorial/
tags:
- kubernetes
- prometheus
- service monitor
title: 'How to create a ServiceMonitor in Microk8s: Observability Tutorial'
---
Hello,


In this quick tutorial we will explore how to create a **ServiceMonitor** in [microk8s](https://microk8s.io/) when you have the **observability** addon enabled.


When you enable the observability addon with:


```
microk8s enable observability
```


It will create the **obserability** namespace and install the Kubernetes Prometheus Stack.


```
 k get pods -n observability
NAME                                                     READY   STATUS    RESTARTS      AGE
kube-prom-stack-prometheus-node-exporter-htzw9           1/1    
tempo-0                                                  2/2     Running   4 (16h ago)   27h
alertmanager-kube-prom-stack-kube-prome-alertmanager-0   2/2  
kube-prom-stack-kube-prome-operator-cbf896985-fb5fc      1/1     
kube-prom-stack-kube-state-metrics-57c8c84df6-hflgj      1/1   
kube-prom-stack-grafana-74d8979894-sjv77                 3/3     
prometheus-kube-prom-stack-kube-prome-prometheus-0       2/2    
loki-0                                                   1/1   
loki-promtail-sg8bv                                      1/1     Running   
```


You will get a [Grafana](https://grafana.com/) configured with a [Prometheus](https://prometheus.io/) data source and other data sources as well.


To monitor a custom service, let’s say a [prometheus metrics exporter for Apache Kafka](https://github.com/danielqsj/kafka_exporter) you will need to create a [ServiceMonitor](https://docs.openshift.com/container-platform/4.10/rest_api/monitoring_apis/servicemonitor-monitoring-coreos-com-v1.html). For the service monitor to be discovered you’ll need to add some labels to it.


To find out the labels you need to add describe the prometheus operator:


```
k describe prometheus/kube-prom-stack-kube-prome-prometheus -n observability
```


```
[...]
  Pod Monitor Namespace Selector:
  Pod Monitor Selector:
    Match Labels:
      Release:  kube-prom-stack
  Service Account Name:  kube-prom-stack-kube-prome-prometheus
  Service Monitor Namespace Selector:
  Service Monitor Selector:
    Match Labels:
      Release:  kube-prom-stack
```


Then create the ServiceMonitor with the corresponding labels: **kube-prom-stack**.


```
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: prometheus-exporter-kafka-monitor
  labels:
    team: observability
    release: kube-prom-stack
spec:
  selector:
    matchLabels:
      app: prometheus-kafka-exporter
      app.kubernetes.io/managed-by: Helm
      chart: prometheus-kafka-exporter-1.8.0
      heritage: Helm
      release: prometheus-exporter
  endpoints:
  - port: exporter-port
```


Thanks for reading!