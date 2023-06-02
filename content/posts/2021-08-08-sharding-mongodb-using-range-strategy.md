---
author: "Denis Nu\u021Biu"
categories:
- Cloud
date: '2021-08-08T13:38:25+00:00'
guid: https://nuculabs.dev/?p=1522
id: 1522
image: /wp-content/uploads/2021/08/23483-scaled.jpg
layout: post
permalink: /2021/08/08/sharding-mongodb-using-range-strategy/
tags:
- database
- how to
- mongo
- mongodb
- scaling
- sharding
- Tutorial
title: Sharding MongoDB using Range strategy
---
Hi 👋👋


In this article I will explore the topic of sharding a Mongo Database that runs on Kubernetes. Before we get started, if you want to follow along, please install the tools listed in the prerequisites section, and if you want to learn more about sharding, check out this fantastic article [Sharding Pattern](https://docs.microsoft.com/en-us/azure/architecture/patterns/sharding).


### Prerequisites


- [Kubernetes Cluster & kubectl](https://kubernetes.io/)
- [mongosh](https://docs.mongodb.com/mongodb-shell/)
- [helm](https://helm.sh/)


## Introduction


Let’s install a MongoDB instance on the Kubernetes cluster using **helm**.


```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-mongo bitnami/mongodb-sharded
```


After the installation completes, save the database’s **root password** and **replica set key**. While doing this the first time I messed up and didn’t save them properly.


Run the following commands to print the password and replica set key on the command line. If you’re on Windows I have provided you with a Powershell function for base64 and if you’re on Unix don’t forget to pass –decode to base64.


```
kubectl get secret --namespace default my-release-mongodb-sharded -o jsonpath="{.data.mongodb-root-password}" | base64
kubectl get secret --namespace default my-release-mongodb-sharded -o jsonpath="{.data.mongodb-replica-set-key}" | base64
```


## Sharding the Database


Verify that all your pods are running and start a shell connection to the mongos server.


```
	@denis ➜ ~ kubectl get pods
	NAME                                              READY   STATUS    RESTARTS   AGE
	my-mongo-mongodb-sharded-configsvr-0              1/1     Running   0          3m8s
	my-mongo-mongodb-sharded-configsvr-1              1/1     Running   0          116s
	my-mongo-mongodb-sharded-mongos-c4dd66768-dqlbv   1/1     Running   0          3m8s
	my-mongo-mongodb-sharded-shard0-data-0            1/1     Running   0          3m8s
	my-mongo-mongodb-sharded-shard0-data-1            1/1     Running   0          103s
	my-mongo-mongodb-sharded-shard1-data-0            1/1     Running   0          3m8s
my-mongo-mongodb-sharded-shard1-data-1            1/1     Running   0          93s
```


```
kubectl port-forward --namespace default svc/my-mongo-mongodb-sharded 27017:27017
# and in another terminal:
mongosh --host 127.0.0.1 --authenticationDatabase admin -u root -p $MONGODB_ROOT_PASSWORD
```


By running **sh.status()** you should get an output which contains two mongo shards:


```
shards
[
  {
    _id: 'my-mongo-mongodb-sharded-shard-0',
    host: 'my-mongo-mongodb-sharded-shard-0/my-mongo-mongodb-sharded-shard0-data-0.my-mongo-mongodb-sharded-headless.default.svc.cluster.local:27017,my-mongo-mongodb-sharded-shard0-data-1.my-mongo-mongodb-sharded-headless.default.svc.cluster.local:27017',
    state: 1
  },
  {
    _id: 'my-mongo-mongodb-sharded-shard-1',
    host: 'my-mongo-mongodb-sharded-shard-1/my-mongo-mongodb-sharded-shard1-data-0.my-mongo-mongodb-sharded-headless.default.svc.cluster.local:27017,my-mongo-mongodb-sharded-shard1-data-1.my-mongo-mongodb-sharded-headless.default.svc.cluster.local:27017',
    state: 1
  }
]
```


To enable sharding on the database and collection, I’m going to insert some dummy data in **my\_data** database and **my\_users** collections. The script used to insert the data is attached at the end of this blog post.


```
[direct: mongos]> sh.enableSharding("my_data")
{
  ok: 1,
  operationTime: Timestamp(3, 1628345449),
  '$clusterTime': {
    clusterTime: Timestamp(3, 1628345449),
    signature: {
      hash: Binary(Buffer.from("e57c8c37047f7aa170fb59f6b11e22aa65159a30", "hex"), 0),
      keyId: Long("6993682727694237708")
    }
  }
}

[direct: mongos]> db.my_users.createIndex({"t": 1})
[direct: mongos]> sh.shardCollection("my_data.my_users", { "t": 1 })

sh.addShardToZone("my-mongo-mongodb-sharded-shard-1", "TSR1")
sh.addShardToZone("my-mongo-mongodb-sharded-shard-0", "TSR2")
```


If you’ve made it this far, congrats, you’ve enabled sharding, now let’s define some rules.


Since we’re going to use a range sharding strategy based on the key t, and I have two shards available I would like my data to be distributed in the following way:


```
 sh.updateZoneKeyRange("my_data.my_users", {t: 46}, {t: MaxKey()}, "TSR2")
 sh.updateZoneKeyRange("my_data.my_users", {t: MinKey()}, {t: 46}, "TSR1")
```


![](/wp-content/uploads/2021/08/mongodb_sharding_diagram_1-1.png?w=855)
*Note: The label on the TSR2 Zone is wrong, the correct value is: **46 ≤ t < 1000***


Running **sh.status()** should now yield the following output.


```
    collections: {
      'my_data.my_users': {
        shardKey: { t: 1 },
        unique: false,
        balancing: true,
        chunkMetadata: { shard: 'my-mongo-mongodb-sharded-shard-1', nChunks: 3 },
        chunks: [
          {
            min: { t: MinKey() },
            max: { t: 45 },
            'on shard': 'my-mongo-mongodb-sharded-shard-1',
            'last modified': Timestamp(2, 1)
          },
          {
            min: { t: 46 },
            max: { t: MaxKey() },
            'on shard': 'my-mongo-mongodb-sharded-shard-0',
            'last modified': Timestamp(0, 2)
          }
        ],
        tags: [
          { tag: 'TSR1', min: { t: MinKey() }, max: { t: 46} },
          { tag: 'TSR2', min: { t: 46 }, max: { t: MaxKey() } }
        ]
      }
```


To test the rules, use the provided python script, modify the **times** variable and run it with various values.


You can run **db.my\_users.getShardDistribution()** to view the data distribution on the shards.


```
[direct: mongos]> db.my_users.getShardDistribution()

Shard my-mongo-mongodb-sharded-shard-0 at my-mongo-mongodb-sharded-shard-0/my-mongo-mongodb-sharded-shard0-data-0.my-mongo-mongodb-sharded-headless.default.svc.cluster.local:27017,my-mongo-mongodb-sharded-shard0-data-1.my-mongo-mongodb-sharded-headless.default.svc.cluster.local:27017
{
  data: '144KiB',
  docs: 1667,
  chunks: 1,
  'estimated data per chunk': '144KiB',
  'estimated docs per chunk': 1667
}

Shard my-mongo-mongodb-sharded-shard-1 at my-mongo-mongodb-sharded-shard-1/my-mongo-mongodb-sharded-shard1-data-0.my-mongo-mongodb-sharded-headless.default.svc.cluster.local:27017,my-mongo-mongodb-sharded-shard1-data-1.my-mongo-mongodb-sharded-headless.default.svc.cluster.local:27017
{
  data: '195KiB',
  docs: 2336,
  chunks: 3,
  'estimated data per chunk': '65KiB',
  'estimated docs per chunk': 778
}
```


## Adding More Shards


To add more shards to the cluster all we need to do is run **helm upgrade**, if you don’t mess up the replica set key like I did it should work on the first run.


```
helm upgrade my-mongo bitnami/mongodb-sharded --set shards=3,configsvr.replicas=2,shardsvr.dataNode.replicas=2,mongodbRootPassword=tcDMM5sqNC,replicaSetKey=D6BGM2ixd3
```


If you mess up the key 😅, then to solve the issue and bring your cluster back online follow these steps.


1. downgrade the cluster back to 2 shards
2. SSH into an old working shard **shard1** or **shard0**, and grab the credentials from the environment variables.


The kubernetes secret and mongos pod’s credential have been overridden by the upgrade and they are wrong!


MONGODB\_ROOT\_PASSWORD=tcDMM5sqNC  
MONGODB\_ENABLE\_DIRECTORY\_PER\_DB=no  
MONGODB\_SYSTEM\_LOG\_VERBOSITY=0  
MY\_MONGO\_MONGODB\_SHARDED\_SERVICE\_PORT=27017  
KUBERNETES\_SERVICE\_HOST=10.245.0.1  
MONGODB\_REPLICA\_SET\_KEY=D6BGM2ixd3


After you save the correct password and replica set key, search for the volumes that belong to the shards which have the wrong replica set key and delete them. In my case I only delete the volumes which belong to the 3rd shard that I’ve added, since counting starts from 0, I’m looking for **shard2** in the name.


```
@denis ➜ Downloads kubectl get persistentvolumeclaims
NAME                                               STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS       AGE
datadir-my-mongo-mongodb-sharded-configsvr-0       Bound    pvc-8e7fa303-9198-419e-a6c1-8de3e6d89962   8Gi        RWO            do-block-storage   132m
datadir-my-mongo-mongodb-sharded-configsvr-1       Bound    pvc-6e3bc70f-83a8-4e80-b856-c44a4295be35   8Gi        RWO            do-block-storage   131m
datadir-my-mongo-mongodb-sharded-shard0-data-0     Bound    pvc-f66647bc-ee3b-4820-b466-a11b197fde74   8Gi        RWO            do-block-storage   132m
datadir-my-mongo-mongodb-sharded-shard0-data-1     Bound    pvc-62257e91-d461-4ddb-af37-4876d2431703   8Gi        RWO            do-block-storage   131m
datadir-my-mongo-mongodb-sharded-shard1-data-0     Bound    pvc-9a062ba5-f320-49c9-ae15-d75e8e5f2cf8   8Gi        RWO            do-block-storage   132m
datadir-my-mongo-mongodb-sharded-shard1-data-1     Bound    pvc-068b04bd-8875-40d7-b47c-40092ceb7973   8Gi        RWO            do-block-storage   130m
datadir-my-mongo-mongodb-sharded-shard2-data-0     Bound    pvc-93d9a238-ae36-49e1-b0b6-f320baf89373   8Gi        RWO            do-block-storage   73m
datadir-my-mongo-mongodb-sharded-shard2-data-1     Bound    pvc-b09a8d0d-5012-4f23-8096-a713f3025521   8Gi        RWO            do-block-storage   50m
@denis ➜ Downloads kubectl get persistentvolumes
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                                      STORAGECLASS       REASON   AGE
pvc-068b04bd-8875-40d7-b47c-40092ceb7973   8Gi        RWO            Delete           Bound    default/datadir-my-mongo-mongodb-sharded-shard1-data-1     do-block-storage            131m
pvc-321136d8-8a27-45cb-8ed1-8d636c530859   8Gi        RWO            Delete           Bound    default/datadir-my-release-mongodb-sharded-shard2-data-1   do-block-storage            143m
pvc-42dd7167-5836-4e94-bf42-473c6cea49a4   8Gi        RWO            Delete           Bound    default/datadir-my-release-mongodb-sharded-shard2-data-0   do-block-storage            145m
pvc-48714777-97b3-4acc-8562-7b69a8e3b488   8Gi        RWO            Delete           Bound    default/datadir-my-release-mongodb-sharded-shard1-data-1   do-block-storage            143m
pvc-499797e9-a5df-4c7b-a1fb-482c3dca36a6   8Gi        RWO            Delete           Bound    default/datadir-my-release-mongodb-sharded-shard3-data-1   do-block-storage            143m
pvc-61ec9e04-1bad-4312-ba16-fb24c12efb4b   8Gi        RWO            Delete           Bound    default/datadir-my-release-
...
```


After that’s done, run the **helm upgrade** command again and if everything is working get a mongosh connection 😀.


Running **sh.status()** will now show the 3rd shard.


```
[
  {
    _id: 'my-mongo-mongodb-sharded-shard-0',
    host: 'my-mongo-mongodb-sharded-shard-0/my-mongo-mongodb-sharded-shard0-data-0.my-mongo-mongodb-sharded-headless.default.svc.cluster.local:27017,my-mongo-mongodb-sharded-shard0-data-1.my-mongo-mongodb-sharded-headless.default.svc.cluster.local:27017',
    state: 1,
    tags: [ 'TSR2' ]
  },
  {
    _id: 'my-mongo-mongodb-sharded-shard-1',
    host: 'my-mongo-mongodb-sharded-shard-1/my-mongo-mongodb-sharded-shard1-data-0.my-mongo-mongodb-sharded-headless.default.svc.cluster.local:27017,my-mongo-mongodb-sharded-shard1-data-1.my-mongo-mongodb-sharded-headless.default.svc.cluster.local:27017',
    state: 1,
    tags: [ 'TSR1' ]
  },
  {
    _id: 'my-mongo-mongodb-sharded-shard-2',
    host: 'my-mongo-mongodb-sharded-shard-2/my-mongo-mongodb-sharded-shard2-data-0.my-mongo-mongodb-sharded-headless.default.svc.cluster.local:27017,my-mongo-mongodb-sharded-shard2-data-1.my-mongo-mongodb-sharded-headless.default.svc.cluster.local:27017',
    state: 1
  }
]
```


Next, update the sharding rules and everything will be working as in the diagram.


```
sh.addShardToZone("my-mongo-mongodb-sharded-shard-2", "TSR3")
sh.removeRangeFromZone("my_data.my_users", {t: 46}, {t: MaxKey()}, "TSR2")
sh.updateZoneKeyRange("my_data.my_users", {t: 46}, {t 1000}, "TSR2")
sh.updateZoneKeyRange("my_data.my_users", {t: 1000}, {t: MaxKey()}, "TSR3")
```


sh.status() should show something like


```
        chunks: [
          {
            min: { t: MinKey() },
            max: { t: 46 },
            'on shard': 'my-mongo-mongodb-sharded-shard-1',
            'last modified': Timestamp(0, 5)
          },
          {
            min: { t: 46 },
            max: { t: 1000 },
            'on shard': 'my-mongo-mongodb-sharded-shard-0',
            'last modified': Timestamp(3, 4)
          },
          {
            min: { t: 1000 },
            max: { t: MaxKey() },
            'on shard': 'my-mongo-mongodb-sharded-shard-2',
            'last modified': Timestamp(1, 5)
          }
        ],
        tags: [
          { tag: 'TSR1', min: { t: MinKey() }, max: { t: 46 } },
          { tag: 'TSR2', min: { t: 46 }, max: { t: 1000 } },
          { tag: 'TSR3', min: { t: 1000 }, max: { t: MaxKey() } }
        ]
      }
```


![](/wp-content/uploads/2021/08/mongodb_sharding_diagram_2.png?w=1024)
## Conclusions


Shading a MongoDB can seem intimidating at first, but with some practice in advance you can do it! If sharding doesn’t work out for you, you can [Convert Sharded Cluster to Replica Set](https://docs.mongodb.com/manual/tutorial/convert-sharded-cluster-to-replica-set/), but, be prepared with some backups.


Thanks for reading 📚 and happy hacking! 🔩🔨


##### Base64 Powershell Function


```
function global:Convert-From-Base64 {
  [CmdletBinding()]
  [Alias('base64')]
  param (
    [parameter(ValueFromPipeline,Mandatory=$True,Position=0)]
    [string] $EncodedText
  )
  process {
    [System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String($EncodedText))
  }
}
```


#### Python Script


```
import random

import pymongo


def do_stuff():
    client = pymongo.MongoClient("mongodb://root:tcDMM5sqNC@127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000")
    col = client.my_data.my_users

    usernames = ["dovahkiin", "rey", "dey", "see", "mee", "rollin", "they", "hating"]
    hobbies = ["coding", "recording", "streaming", "batman", "footbal", "sports", "mathematics"]
    ages = [18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28]
    # times = [12, 14, 15, 23, 45, 32, 20]
    times = [47, 80, 93, 49, 96, 43]

    buffer = []
    for _ in range(1_000):
        first = random.choice(usernames).capitalize()
        mid = random.choice(usernames).capitalize()
        last = random.choice(usernames).capitalize()
        buffer.append(pymongo.InsertOne({
            "name": f"{first} '{mid}' {last}",
            "age": random.choice(ages),
            "hobbies": random.choice(hobbies),
            "t": random.choice(times)
        }))
    col.bulk_write(buffer)


if __name__ == '__main__':
    do_stuff()
```


## References














[Blue vector created by starline – www.freepik.com](https://www.freepik.com/vectors/blue)