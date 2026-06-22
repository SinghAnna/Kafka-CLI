# 🚀 Kafka CLI Guide (A to Z)

## 📌 Producer, Topic, Partition and Consumer Group

```text
Producer ───► Topic ───► Consumer Group ───► Consumers
                 │
                 ├── Partition-0 ───► Consumer-1
                 ├── Partition-1 ───► Consumer-2
                 └── Partition-2 ───► Consumer-3
```

### Important Rules

* A topic can have multiple partitions.
* A consumer group can have multiple consumers.
* One partition is consumed by only one consumer in a group.
* Different consumer groups can read the same messages independently.

---

# 📝 Topic Commands

## Create Topic

```bash
docker exec -it kafka kafka-topics \
--create \
--topic orders \
--bootstrap-server localhost:9092 \
--partitions 3 \
--replication-factor 1
```

## List Topics

```bash
docker exec -it kafka kafka-topics \
--list \
--bootstrap-server localhost:9092
```

## Describe Topic

```bash
docker exec -it kafka kafka-topics \
--describe \
--topic orders \
--bootstrap-server localhost:9092
```

## Delete Topic

```bash
docker exec -it kafka kafka-topics \
--delete \
--topic orders \
--bootstrap-server localhost:9092
```

## Increase Partitions

```bash
docker exec -it kafka kafka-topics \
--alter \
--topic orders \
--partitions 5 \
--bootstrap-server localhost:9092
```

---

# ✍️ Producer Commands

## Start Producer

```bash
docker exec -it kafka kafka-console-producer \
--topic orders \
--bootstrap-server localhost:9092
```

Example:

```text
order-1
order-2
order-3
```

## Producer with Key

```bash
docker exec -it kafka kafka-console-producer \
--topic orders \
--bootstrap-server localhost:9092 \
--property parse.key=true \
--property key.separator=:
```

Example:

```text
101:Mobile
102:Laptop
103:Keyboard
```

---

# 📥 Consumer Commands

## Read Latest Messages

```bash
docker exec -it kafka kafka-console-consumer \
--topic orders \
--bootstrap-server localhost:9092
```

## Read From Beginning

```bash
docker exec -it kafka kafka-console-consumer \
--topic orders \
--from-beginning \
--bootstrap-server localhost:9092
```

## Show Message Keys

```bash
docker exec -it kafka kafka-console-consumer \
--topic orders \
--from-beginning \
--property print.key=true \
--bootstrap-server localhost:9092
```

## Read Specific Partition

```bash
docker exec -it kafka kafka-console-consumer \
--topic orders \
--partition 0 \
--bootstrap-server localhost:9092
```

## Read Limited Messages

```bash
docker exec -it kafka kafka-console-consumer \
--topic orders \
--from-beginning \
--max-messages 5 \
--bootstrap-server localhost:9092
```

---

# 👥 Consumer Groups

## How is a Consumer Group Created?

Consumer groups are created automatically.

Start a consumer with a group:

```bash
docker exec -it kafka kafka-console-consumer \
--topic orders \
--group order-group \
--bootstrap-server localhost:9092
```

Kafka automatically creates:

```text
Consumer Group : order-group
```

---

## Start Multiple Consumers in Same Group

Terminal 1:

```bash
docker exec -it kafka kafka-console-consumer \
--topic orders \
--group order-group \
--bootstrap-server localhost:9092
```

Terminal 2:

```bash
docker exec -it kafka kafka-console-consumer \
--topic orders \
--group order-group \
--bootstrap-server localhost:9092
```

Kafka distributes partitions among consumers.

---

## List Consumer Groups

```bash
docker exec -it kafka kafka-consumer-groups \
--list \
--bootstrap-server localhost:9092
```

## Describe Consumer Group

```bash
docker exec -it kafka kafka-consumer-groups \
--describe \
--group order-group \
--bootstrap-server localhost:9092
```

## Delete Consumer Group

```bash
docker exec -it kafka kafka-consumer-groups \
--delete \
--group order-group \
--bootstrap-server localhost:9092
```

---

# ⚙️ Broker Configurations

## View Config Help

```bash
docker exec -it kafka kafka-configs --help
```

## Describe Broker Configurations

```bash
docker exec -it kafka kafka-configs \
--bootstrap-server localhost:9092 \
--entity-type brokers \
--entity-name 1 \
--describe
```

## Change Broker Configuration

```bash
docker exec -it kafka kafka-configs \
--bootstrap-server localhost:9092 \
--entity-type brokers \
--entity-name 1 \
--alter \
--add-config log.cleaner.threads=2
```

---

# 📊 Offsets

## Get Consumer Group Offsets

```bash
docker exec -it kafka kafka-consumer-groups \
--group order-group \
--describe \
--bootstrap-server localhost:9092
```

---

# 🔍 Cluster Information

## Metadata Information

```bash
docker exec -it kafka kafka-broker-api-versions \
--bootstrap-server localhost:9092
```

## Display Cluster Id

```bash
docker exec -it kafka kafka-cluster \
--bootstrap-server localhost:9092 \
cluster-id
```

---

# 🧹 Administration Commands

## Stop Containers

```bash
docker stop kafka zookeeper
```

## Start Containers

```bash
docker start zookeeper kafka
```

## Remove Containers

```bash
docker rm -f kafka zookeeper
```

## Remove Network

```bash
docker network rm kafka-net
```

---

# 📌 Most Important Commands for Interviews

1. kafka-topics
2. kafka-console-producer
3. kafka-console-consumer
4. kafka-consumer-groups
5. kafka-configs
6. kafka-broker-api-versions
7. kafka-cluster
8. kafka-acls
9. kafka-reassign-partitions
10. kafka-delete-records
11. kafka-storage
12. kafka-metadata-quorum

Master these commands first. They cover almost everything needed for Spring Boot + Kafka development and most interviews.
