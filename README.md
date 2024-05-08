# Apache Kafka with KRaft: A ZooKeeper Replacement

## Introduction

Apache Kafka is a distributed streaming platform that allows you to publish and subscribe to streams of records (events). Traditionally, Kafka relied on Apache ZooKeeper for managing its metadata, but recent architectural changes have introduced KRaft as a replacement for ZooKeeper.

### Why Replace ZooKeeper?

In the past, Kafka's controller—a critical component responsible for managing metadata—was implemented using ZooKeeper. However, as Kafka clusters grew in size, scalability bottlenecks related to ZooKeeper's read and write traffic became apparent. To address this, Kafka introduced KRaft, an internal log-based consensus protocol for metadata management.

Here are some reasons why Kafka replaced ZooKeeper with KRaft:

1. **Simplified Deployment**: With KRaft, Kafka no longer requires an external ZooKeeper ensemble. All metadata management happens within Kafka itself.

2. **Enhanced Scalability**: KRaft eliminates the need for ZooKeeper watchers and reduces the load on external ZooKeeper servers. This improves scalability as Kafka clusters grow.

3. **Improved Performance**: By storing metadata internally, Kafka can optimize read and write operations, resulting in better performance.

## Kafka Docker Compose File

```yaml
version: "3"
services:
  kafka:
    image: bitnami/kafka:3.6
    container_name: kafka
    ports:
      - "9092:9092"
    environment:
      - KAFKA_ENABLE_KRAFT=yes # use kraft
      - KAFKA_CFG_PROCESS_ROLES=broker,controller # both roles
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:2181
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://127.0.0.1:9092
      - KAFKA_BROKER_ID=1
      - KAFKA_CFG_NODE_ID=1
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@127.0.0.1:2181 # node_id@controller:port
      - ALLOW_PLAINTEXT_LISTENER=yes
      - KAFKA_KRAFT_CLUSTER_ID=MkU3OEVBNTcwNTJENDM2Qk # Kafka cluster ID when using Kafka Raft mode (KRaft).
    volumes:
      - ./kafka:/bitnami/kafka
```

## Start Kafka

1. Open a terminal and navigate to the directory containing your `docker-compose.yml` file.
2. Run the following command:

```bash
docker-compose up -d
```
