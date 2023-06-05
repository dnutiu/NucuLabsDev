---
author: "Denis Nu\u021Biu"
categories:
- Cloud
- Programming
date: '2022-10-28T19:31:32+00:00'
guid: https://nuculabs.dev/?p=2401
id: 2401
layout: post
permalink: /2022/10/28/apache-flink-checkpoints-on-s3-and-s3-compatible-storage/
tags:
- aws
- checkpoints
- email
- flink
- permission forbidden
- s3
title: Apache Flink Checkpoints on S3 and S3 compatible storage
---
Hello,


Recently someone working at Yahoo emailed me regarding an old [thread ](https://www.mail-archive.com/user@flink.apache.org/msg44262.html)I’ve started on the Apache Flink user mailing list. I’ve replied to the e-mail but also decided to turn the reply into a blog post, because it might help other people as well.


## Email


Hi,


I was able to get it working after tinkering with it. The issue was mainly a miscommunication, we didn’t formally know which authentication method we were using in AWS. We we’re using only s3://


Here are our configuration options:


On S3 compatible storage:


```
fs.s3a.access.key: ""
fs.s3a.secret.key: ""
fs.s3a.connection.ssl.enabled: "false"
fs.s3a.endpoint: "ceph-mcr-1.xxx.xxx.xxx:xxx"
fs.s3a.list.version: "1"
s3.path.style.access: "true"
containerized.master.env.ENABLE_BUILT_IN_PLUGINS: "flink-s3-fs-hadoop-1.13.2.jar"
containerized.taskmanager.env.ENABLE_BUILT_IN_PLUGINS: "flink-s3-fs-hadoop-1.13.2.jar"
state.backend: "rocksdb"
state.backend.incremental: "true"
state.checkpoints.dir: "s3://bucket-name/checkpoints/$cluster_name$/"
state.savepoints.dir: "s3://bucket-name/savepoints/$cluster_name$/"
```


On S3 with AWS:


```
fs.s3a.aws.credentials.provider: "com.amazonaws.auth.WebIdentityTokenCredentialsProvider",
containerized.master.env.ENABLE_BUILT_IN_PLUGINS: "flink-s3-fs-hadoop-1.13.2.jar",
containerized.taskmanager.env.ENABLE_BUILT_IN_PLUGINS: "flink-s3-fs-hadoop-1.13.2.jar",
state.backend: "rocksdb",
state.backend.incremental: "true",
state.checkpoints.dir: "s3://xxx/checkpoints/$cluster_name$/",
state.savepoints.dir: "s3://xxx/savepoints/$cluster_name$/"
```


fs.s3a.aws.credentials.provider was the authentication method (credentials provider) that we were missing, it’s not found in the Hadoop plugin docs\[2\] but it’s found in AWSJavaSDK docs\[3\]\[4\]. AWS mounts secrets inside Flink pods so using this provider should make it work without further configuration.


Note that flink-s3-fs-hadoop-1.13.2.jar needs to be adapted to your Flink version. $cluster\_name should also be substituted with your cluster/deployment name.


That’s pretty much it, I’m also attaching the Flink S3 docs\[1\] to the email. Thanks for reaching out! Hope you’ll figure it out!


Best,


Denis


[docs/deployment/filesystems/s3/](https://nightlies.apache.org/flink/flink-docs-release-1.15/docs/deployment/filesystems/s3/)\[1\]  
[hadoop-aws/tools/hadoop-aws/index.html#S3A](https://hadoop.apache.org/docs/stable/hadoop-aws/tools/hadoop-aws/index.html#S3A)\[2\]  
[amazonaws/auth/AWSCredentialsProvider.html](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/auth/AWSCredentialsProvider.html)\[3\]  
[amazonaws/auth/WebIdentityTokenCredentialsProvider.html](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/auth/WebIdentityTokenCredentialsProvider.html)\[4\]


As a side note, if you’re using the [Flink Operator](https://nightlies.apache.org/flink/flink-kubernetes-operator-docs-stable/) to deploy your Flink job you can set environment variables in the pod template file instead of flink-config.yaml.


Thanks for reading!