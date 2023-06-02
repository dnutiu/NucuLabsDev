---
author: "Denis Nu\u021Biu"
categories:
- Cloud
- Web Development
date: '2020-07-29T00:15:35+00:00'
guid: http://nuculabs.dev/?p=1087
id: 1087
layout: post
permalink: /2020/07/29/kafka-and-kafka-connect-getting-started-quick/
tags:
- data scrapping
- github
- kafka
- kafka connect
- Tutorial
title: Kafka Connect GitHub source connector
---
Hello!


In this article we will discuss how to quickly get started with Kafka and Kafka Connect to grab all the commits from a Github repository.


This is a practical tutorial which saves you some time browsing the Kafka’s documentation.


## Environment


Kafka is bit difficult to setup, you will need [Kafka](https://kafka.apache.org/), [Zookeper ](https://zookeeper.apache.org/)and [Kafka Connect](https://docs.confluent.io/3.0.1/connect/intro.html) at least. To simplify the setup we’re going to use [Docker ](https://www.docker.com/)and docker-compose.


Once you have Docker and docker-compose installed on your system, you can run a single command and get a Kafka environment for developing.


Let’s start Kafka by using the [Confluent Platform repository](https://github.com/confluentinc/cp-all-in-one), make sure you have Docker, Docker-Compose and Git installed before proceding.


```
git clone https://github.com/confluentinc/cp-all-in-one
cd .\cp-all-in-one\cp-all-in-one-community
docker-compose up
```


After the `docker-compose up` command finishes, Kafka and it’s related components should be up and running. To make sure all the components are running properly please start a new terminal window and compare your `docker ps `output with mine:


```
CONTAINER ID        IMAGE                                         COMMAND                  CREATED             STATUS                            PORTS                                                                      NAMES
fc95d4828397        confluentinc/ksqldb-examples:5.5.1            "bash -c 'echo Waiti…"   6 minutes ago       Up 6 minutes
                 ksql-datagen
28e67d645888        confluentinc/cp-ksqldb-cli:5.5.1              "/bin/sh"                6 minutes ago       Up 6 minutes
                 ksqldb-cli
371fd742ad1f        confluentinc/cp-ksqldb-server:5.5.1           "/etc/confluent/dock…"   6 minutes ago       Up 6 minutes (healthy)            0.0.0.0:8088->8088/tcp
                 ksqldb-server
caeb86c71308        cnfldemos/kafka-connect-datagen:0.3.2-5.5.0   "/etc/confluent/dock…"   6 minutes ago       Up 6 minutes (health: starting)   0.0.0.0:8083->8083/tcp, 9092/tcp
                 connect
c4d90361c677        confluentinc/cp-kafka-rest:5.5.1              "/etc/confluent/dock…"   6 minutes ago       Up 6 minutes                      0.0.0.0:8082->8082/tcp
                 rest-proxy
c3752a0c1487        confluentinc/cp-schema-registry:5.5.1         "/etc/confluent/dock…"   6 minutes ago       Up 6 minutes                      0.0.0.0:8081->8081/tcp
                 schema-registry
14bfbef71822        confluentinc/cp-kafka:5.5.1                   "/etc/confluent/dock…"   6 minutes ago       Up 6 minutes                      0.0.0.0:9092->9092/tcp, 0.0.0.0:9101->9101/tcp, 0.0.0.0:29092->29092/tcp   broker
f2ddb8971efa        confluentinc/cp-zookeeper:5.5.1               "/etc/confluent/dock…"   6 minutes ago       Up 6 minutes                      2888/tcp, 0.0.0.0:2181->2181/tcp, 3888/tcp
                 zookeeper
```


If it looks similar then you’re ready to start configuring Kafka Connect.


## Kafka Connect


Kafka Connect is a great tool for building data pipelines. We’re going to use it to get data from Github into Kafka.


We could write a simple python producer in order to do that, query Github’s API and produce a record for Kafka using a client library, but, Kafka Connect comes with additional benefits. It’s like 
Docker containers
 for Producers and Consumers, in fact, in Kafka Connect, the producers are called **source** connectors and the consumers **sink** connectors.


## Installing the Github connector


To install the Github connector which is supported by Confluent, you have to download it from [here](https://www.confluent.io/hub/confluentinc/kafka-connect-github). (click download zip)


After the download has finished, unzip the files.


Before installing it, if you visit: 
 you will see the currently installed connectors, there’s currently no Github connector.


To install it, we need to copy it’s folder into the Kafka Connect container, and restart it:


```
docker cp ./confluentinc-kafka-connect-github-1.0.1/. connect:/usr/share/java
docker restart connect
```


If we visit[ http://localhost:8083/connector-plugins](http://localhost:8083/connector-plugins) again, we’ll see that the connector has been installed successfully:


```
{"class":"io.confluent.connect.github.GithubSourceConnector","type":"source","version":"1.0.1"},
```


## Configuring the connector


There’s not configuration for the connector thus the connector isn’t running. To configured a connector you need to make a post request to the configuration endpoint, you can have as many configuration as you like for the connector, each configuration represents a worker in it’s own, the catch is that you need to use a different name.


To view the already configured connectors we can visit: [localhost:8083/connectors/](8083/connectors/). As expected, an empty JSON should be returned.


To configure the Github connector, I took the sample config from it’s [docummentation](https://docs.confluent.io/current/connect/kafka-connect-github/#properties-based-example) and adapted it into json format.


 I’ve also chosen to use the JsonConverter instead of the AvroConverter for simplicity, and instead of getting the stargazers I’m getting the commits of the repo instead.


Here’s the config:


```
{
"name":"MyGithubConnector",
"config":{
"connector.class":"io.confluent.connect.github.GithubSourceConnector",
"confluent.topic.bootstrap.servers":"broker:29092",
"tasks.max":"1",
"confluent.topic.replication.factor":"1",
"github.service.url":"https://api.github.com",
"github.repositories":"apache/kafka",
"github.tables":"```
commits
```",
"github.since":"2019-01-01",
"github.access.token":"YOUR_PERSONAL_ACCESS_TOKEN",
"topic.name.pattern":"github-avro-${entityName}",
"key.converter":"org.apache.kafka.connect.json.JsonConverter",
"value.converter":" org.apache.kafka.connect.json.JsonConverter"
}
}
```

Now, we need to upload the config to the Kafka Connect, I saved it as a file named github-connector-config.json and used curl to upload it. If the upload is successful the server should echo back your config:

```
➜ cp-all-in-one-community git:(5.5.1-post) ✗ curl -X POST -H "Content-Type: application/json" --data @github-connector-config.json http://localhost:8083/connectors
{"name":"MyGithubConnector","config":{"connector.class":"io.confluent.connect.github.GithubSourceConnector","confluent.topic.bootstrap.servers":"broker:29092","tasks.max":"1","confluent.topic.replication.factor":"1","github.service.url":"https://api.github.com","github.repositories":"apache/kafka","github.tables":"commits","github.since":"2019-01-01","github.access.token":"xxx","topic.name.pattern":"github-avro-${entityName}","key.converter":"org.apache.kafka.connect.json.JsonConverter","value.converter":" org.apache.kafka.connect.json.JsonConverter","name":"MyGithubConnector"},"tasks":[],"type":"source"}%
```

Visiting <http://localhost:8083/connectors/> now returns our config for our Github connector:

```
["MyGithubConnector"]
```

If we check it’s status (<http://localhost:8083/connectors/MyGithubConnector/status>) we should see that the connector is already running:

```
{"name":"MyGithubConnector","connector":{"state":"RUNNING","worker_id":"connect:8083"},"tasks":[{"id":0,"state":"RUNNING","worker_id":"connect:8083"}],"type":"source"}
```

The connector will pull all the commits it can get, in 5 minutes as I let it ran, It pulled over 2.5k in Github commits.

I will stop it in order to preserve disk space:

```
curl -X PUT localhost:8083/connectors/MyGithubConnector/pause
```

## Inspecting the data in Kafka

To inspect the data I use a tool called [kafkacat](https://github.com/edenhill/kafkacat).

Running `kafkacat -C -b localhost:9092 -t github-avro-commits` should show you the data collected from Github:

```
"commit":{ "author":{ "name":"hejiefang", "email":"xxxx", "date":1546437651000 }, "committer":{ "name":"Manikumar Reddy", "email":"xxxx", "date":1546437651000 }, "message":"MINOR: Fix doc format in upgrade notes\n\nAuthor: hejiefang 
\n\nReviewers: Srinivas 
, Manikumar Reddy 
\n\nCloses #6076 from hejiefang/modifynotable", "tree":{ "sha":"91db6646f829d6636011d09fdc8643e36280716b", "url":"https://api.github.com/repos/apache/kafka/git/trees/91db6646f829d6636011d09fdc8643e36280716b" }, "url":"https://api.github.com/repos/apache/kafka/git/commits/ffd6f2a2e8a573695d0c1c98e663f0b8198b1b6d", "comment_count":0, "verification":{ "verified":false, "reason":"unsigned", "signature":null, "payload":null } },
```


## Summary


We’ve successfully setup a simple Kafka playground with docker-compose and we installed a SOURCE connector for Github. It was fairly easy and Kafka Connect’s rest API is quite powerful and user friendly.


In the next article we will take the commits data and index it in ElasticSearch using a SINK connector.


Thanks for reading!


- [Kafka Connect Reference](https://docs.confluent.io/current/connect/references/index.html)
- [Gihub Source Connector Reference](https://docs.confluent.io/current/connect/kafka-connect-github/index.html)


### Bonus


You can debug the connector by [modifying the logging level](https://rmoff.net/2020/01/16/changing-the-logging-level-for-kafka-connect-dynamically/):


```
// Logging set
curl -s -X PUT -H "Content-Type:application/json" \
http://localhost:8083/admin/loggers/io.confluent.connect.github.GithubSourceConnector \
-d '{"level": "TRACE"}' \
| jq '.'
```