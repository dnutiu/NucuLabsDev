---
author: "Denis Nu\u021Biu"
categories:
- Cloud
date: '2021-09-19T15:13:29+00:00'
guid: http://nuculabs.dev/?p=1131
id: 1131
layout: post
permalink: /2021/09/19/kafka-connect-mongodb-sink-connector/
tags:
- connector
- how to
- kafka
- kafka connect
- mongodb
- sink
- Tutorial
title: Kafka Connect MongoDB Sink Connector
---
Hello 👋,


In this article we’re going to build a data pipeline that connects Kafka to MongoDB.


In short, we’re going to add a MongoDB Sink connector to a Kafka Connect cluster and run a MongoDB instance in Docker to test the connector.


By reading this article I hope that you will learn


- How to install the MongoDB connector in Kafka Connect
- How to configure the MongoDB connector
- How to create topics in Kafka using Confluent Tools
- How to monitor Kafka Connect using JConsole.


Let’s get started!


## Running MongoDB with Docker Compose 🚢


Confluent provides us with a [docker-compose](https://docs.docker.com/compose/) file that already contains everything we need, except for some minor tweaks.


Please download the following file and open it in your favorite editor: 
.


Apply the following edits to the file, you can replace the **connect** block and append the **mongodb** block at the end of the file.


```
 
  connect:
    image: cnfldemos/kafka-connect-datagen:0.5.0-6.2.0
    hostname: connect
    container_name: connect
    depends_on:
      - broker
      - schema-registry
    ports:
      - "8083:8083"
      - "9102:9102"
    environment:
      CONNECT_BOOTSTRAP_SERVERS: 'broker:29092'
      CONNECT_REST_ADVERTISED_HOST_NAME: connect
      CONNECT_REST_PORT: 8083
      CONNECT_GROUP_ID: compose-connect-group
      CONNECT_CONFIG_STORAGE_TOPIC: docker-connect-configs
      CONNECT_CONFIG_STORAGE_REPLICATION_FACTOR: 1
      CONNECT_OFFSET_FLUSH_INTERVAL_MS: 10000
      CONNECT_OFFSET_STORAGE_TOPIC: docker-connect-offsets
      CONNECT_OFFSET_STORAGE_REPLICATION_FACTOR: 1
      CONNECT_STATUS_STORAGE_TOPIC: docker-connect-status
      CONNECT_STATUS_STORAGE_REPLICATION_FACTOR: 1
      CONNECT_KEY_CONVERTER: org.apache.kafka.connect.storage.StringConverter
      CONNECT_VALUE_CONVERTER: org.apache.kafka.connect.storage.StringConverter
      CONNECT_VALUE_CONVERTER_SCHEMA_REGISTRY_URL: http://schema-registry:8081
      CONNECT_PLUGIN_PATH: "/usr/share/java,/usr/share/confluent-hub-components"
      CONNECT_LOG4J_LOGGERS: org.apache.zookeeper=ERROR,org.I0Itec.zkclient=ERROR,org.reflections=ERROR
      KAFKA_JMX_PORT: 9102
      KAFKA_JMX_HOSTNAME: localhost


  mongodb:
    image: mongo:4.2-rc-bionic
    hostname: mongodb
    container_name: mongodb
    depends_on:
      - broker
      - connect
    ports:
      - 27017:27017
```


Run **docker-compose up** to start all services and verify that everything is running with **docker ps**:


```
CONTAINER ID   IMAGE                                         COMMAND                  CREATED          STATUS
         PORTS
                       NAMES
95165f0156f4   confluentinc/cp-ksqldb-cli:6.2.0              "/bin/sh"                37 minutes ago   Up 37 minutes    
                       ksqldb-cli
ecc4cde0f30b   confluentinc/ksqldb-examples:6.2.0            "bash -c 'echo Waiti…"   37 minutes ago   Up 37 minutes    
                       ksql-datagen
962204b34543   mongo:4.2-rc-bionic                           "docker-entrypoint.s…"   37 minutes ago   Up 37 minutes             0.0.0.0:27017->27017/tcp, :::27017->27017/tcp
                       mongodb
c950f33f501a   confluentinc/cp-ksqldb-server:6.2.0           "/etc/confluent/dock…"   37 minutes ago   Up 37 minutes             0.0.0.0:8088->8088/tcp, :::8088->8088/tcp
                       ksqldb-server
3527577701d3   confluentinc/cp-kafka-rest:6.2.0              "/etc/confluent/dock…"   37 minutes ago   Up 37 minutes             0.0.0.0:8082->8082/tcp, :::8082->8082/tcp
                       rest-proxy
ca69f204f4bb   cnfldemos/kafka-connect-datagen:0.5.0-6.2.0   "/etc/confluent/dock…"   37 minutes ago   Up 31 minutes (healthy)   0.0.0.0:8083->8083/tcp, :::8083->8083/tcp, 0.0.0.0:9102->9102/tcp, :::9102->9102/tcp, 9092/tcp
                       connect
aeaea67059c3   confluentinc/cp-schema-registry:6.2.0         "/etc/confluent/dock…"   37 minutes ago   Up 37 minutes             0.0.0.0:8081->8081/tcp, :::8081->8081/tcp
                       schema-registry
b9a761b98a49   confluentinc/cp-kafka:6.2.0                   "/etc/confluent/dock…"   37 minutes ago   Up 37 minutes             0.0.0.0:9092->9092/tcp, :::9092->9092/tcp, 0.0.0.0:9101->9101/tcp, :::9101->9101/tcp, 0.0.0.0:29092->29092/tcp, :::29092->29092/tcp   broker
ca63570b60d4   confluentinc/cp-zookeeper:6.2.0               "/etc/confluent/dock…"   37 minutes ago   Up 37 minutes             2888/tcp, 0.0.0.0:2181->2181/tcp, :::2181->2181/tcp, 3888/tcp
                       zookeeper
```


## Installing the MongoDB Sink Connector on Kafka Connect 🌠


You may download the connector directly from Github [mongodb/mongo-kafka/releases/tag/r1.6.0](https://github.com/mongodb/mongo-kafka/releases/tag/r1.6.0).


Click on [](https://github.com/mongodb/mongo-kafka/releases/download/r1.6.0/mongodb-kafka-connect-mongodb-1.6.0.zip)[mongodb-kafka-connect-mongodb-1.6.0.zip](https://github.com/mongodb/mongo-kafka/releases/download/r1.6.0/mongodb-kafka-connect-mongodb-1.6.0.zip) then unzip it and copy the directory into the plugin path /usr/share/java as defined in the **CONNECT\_PLUGIN\_PATH: “/usr/share/java,/usr/share/confluent-hub-components”** environment variable.


To copy it you can run:


```
docker cp .\mongodb-kafka-connect-mongodb-1.6.0\ connect:/usr/share/java/
 docker restart connect
connect
```


Connect needs to be restarted to pick-up the newly installed plugin. Verify that the connector plugin has been successfully installed:


```
➜  bin curl -s -X GET http://localhost:8083/connector-plugins | jq | head -n 20
[
  {
    "class": "com.mongodb.kafka.connect.MongoSinkConnector",
    "type": "sink",
    "version": "1.6.0"
  },
  {
    "class": "com.mongodb.kafka.connect.MongoSourceConnector",
    "type": "source",
    "version": "1.6.0"
  },
```


Note: If you don’t have [jq](https://stedolan.github.io/jq/) installed you can omit it.


## Creating the topics 


Before starting the connector, let’s create the Kafka Topics **events** and **events.deadletter**, they will be used them in the connector.


To create the topics, we will need to download Confluent tools and run **kafka-topics**.


```
curl -s -O http://packages.confluent.io/archive/6.2/confluent-community-6.2.0.tar.gz
tar -xzf .\confluent-community-6.2.0.tar.gz
cd .\confluent-6.2.0\bin\


 ./kafka-topics --bootstrap-server localhost:9092 --list
__consumer_offsets
__transaction_state
_confluent-ksql-default__command_topic
_schemas
default_ksql_processing_log
docker-connect-configs
docker-connect-offsets
docker-connect-status


./kafka-topics --bootstrap-server localhost:9092 --create --topic events --partitions 3
Created topic events.


./kafka-topics --bootstrap-server localhost:9092 --create --topic events.deadletter --partitions 3
WARNING: Due to limitations in metric names, topics with a period ('.') or underscore ('_') could collide. To avoid issues, it is best to use either, but not both.
Created topic events.deadletter.
```


**
Note
**: You will need Java to run the Confluent tools if you’re on Ubuntu you can type `sudo apt install openjdk-8-jdk`.


## Starting the connector 🚙


To start the connector, it is enough to do a single post request to the [connector’s API](https://docs.confluent.io/platform/current/connect/references/restapi.html#post--connectors) with the connector’s [configuration](https://docs.mongodb.com/kafka-connector/current/kafka-sink-properties/).


The configuration that we will use is going to be:


```
curl --request POST \
  --url http://localhost:8083/connectors \
  --header 'Content-Type: application/json' \
  --data '{
	"name": "mongo-sink-connector",
	"config": {
		"connector.class": "com.mongodb.kafka.connect.MongoSinkConnector",
		"tasks.max": "1",
		"topics": "events",
		"connection.uri": "mongodb://mongodb:27017/my_events",
		"database": "my_events",
		"collection": "kafka_events",
		"max.num.retries": 5,
		"mongo.errors.tolerance": "all",
		"mongo.errors.log.enable": true,
		"errors.log.include.messages": true,
		"errors.deadletterqueue.topic.name": "events.deadletter",
		"errors.deadletterqueue.context.headers.enable": true,
	}
}'
```


In short, this POST will create a new connector named **mongo-sink-connector** using the com.mongodb.kafka.connect.MongoSinkConnector java class, run a single connector task that will get all the messages from the **events** topic and put them into the Mongo found at **mongodb://mongodb:27017/my\_events**, database named **my\_events** and collection named **kafka\_events**. The records which will fail to be written into the database will be placed on a[ dead letter](https://en.wikipedia.org/wiki/Dead_letter_queue) topic named events.deadletter, in my opinion this is better than discarding them, since we can inspect the topic to see what went wrong.


To verify that the connector is running, you can retrieve its first tasks status with:


```
➜  bin curl -s -X GET http://localhost:8083/connectors/mongo-sink-connector/tasks/0/status | jq
{
  "id": 0,
  "state": "RUNNING",
  "worker_id": "connect:8083"
}
```


## Querying the Database 🗃


Now that our Kafka Connect cluster is running and is configured, all that’s left to do is POST some dummy data into Kafka and check for it in the database.


```
curl --request POST \
  --url http://localhost:8082/topics/events \
  --header 'Content-Type: application/vnd.kafka.json.v2+json' \
  --data '{
	"records": [
		{
			"key": "somekey",
			"value": {
				"glossary": {
					"title": "example glossary",
					"GlossDiv": {
						"title": "S",
						"GlossList": {
							"GlossEntry": {
								"ID": "SGML",
								"SortAs": "SGML",
								"GlossTerm": "Standard Generalized Markup Language",
								"Acronym": "SGML",
								"Abbrev": "ISO 8879:1986",
								"GlossDef": {
									"para": "A meta-markup language, used to create markup languages such as DocBook.",
									"GlossSeeAlso": [
										"GML",
										"XML"
									]
								},
								"GlossSee": "markup"
							}
						}
					}
				}
			}
		}
	]
}'
```


That’s all! 🎉If we now connect to the database using mongosh or any other client, we can query the data.


```
mongosh
> use my_events
switched to db my_events
> db.kafka_events.findOne()
{
  _id: ObjectId("6147242856623b0098fc756d"),
  glossary: {
    title: 'example glossary',
    GlossDiv: {
      title: 'S',
      GlossList: {
        GlossEntry: {
          ID: 'SGML',
          SortAs: 'SGML',
          GlossTerm: 'Standard Generalized Markup Language',
          Acronym: 'SGML',
          Abbrev: 'ISO 8879:1986',
          GlossDef: {
            para: 'A meta-markup language, used to create markup languages such as DocBook.',
            GlossSeeAlso: [ 'GML', 'XML' ]
          },
          GlossSee: 'markup'
        }
      }
    }
  }
}
```


## Viewing Kafka Connect JMX Metrics


J[Console ](https://docs.oracle.com/javase/7/docs/technotes/guides/management/jconsole.html)is a tool that can be used to view JMX metrics exposed by Kafka Connect, if you installed openjdk-8 it should come with it


- 
![](/wp-content/uploads/2021/09/image-1.png)


Start JConsole and connect to **localhost:9102**. If you get a warning about an insecure connection, accept the connection, and ignore it.


After you’re connected click the MBeans tab and explore 🦹‍♀️


- 
![](/wp-content/uploads/2021/09/image-2.png)


## Summary


Getting into Kafka and Kafka Connect can be a bit overwhelming at first. I hope that this tutorial has provided you with the necessary basics so you can continue to play and explore on your own.


Spinning up a playground for Kafka and Connect using docker-compose isn’t that complicated, you can start from the [confluent-cp-community](https://github.com/confluentinc/cp-all-in-one/tree/6.2.0-post/cp-all-in-one-community) repo, it will give you everything you need to get started. With some little modifications to the docker-compose file, we’ve spawned a MongoDB instance and exposed the JMX metrics in Kafka Connect.


Next, we’ve installed and configured the [MongoDB connector](https://www.mongodb.com/kafka-connector) and confirmed that it works as expected.


If you have any questions let me know in the comments.


Until next time! 🍻