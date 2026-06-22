# 🚀 Kafka Setup Using Docker

Apache Kafka and Zookeeper setup using Docker along with commonly used Kafka CLI commands.

## 📋 Prerequisites

* Docker Installed
* Docker Desktop Running
* Basic knowledge of Kafka Topics, Producers, and Consumers

---

## 🏗️ Architecture

```text
Producer ---> Kafka Topic ---> Consumer
                  |
                  |
             Zookeeper
```

---

## Step 1: Create Docker Network

```bash
docker network create kafka-net
```

---

## Step 2: Start Zookeeper

```bash
docker run -d \
  --name zookeeper \
  --network kafka-net \
  -p 2181:2181 \
  -e ZOOKEEPER_CLIENT_PORT=2181 \
  confluentinc/cp-zookeeper:7.4.0
```

---

## Step 3: Start Kafka

```bash
docker run -d \
  --name kafka \
  --network kafka-net \
  -p 9092:9092 \
  -e KAFKA_BROKER_ID=1 \
  -e KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 \
  -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092 \
  -e KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1 \
  confluentinc/cp-kafka:7.4.0
```

---

## ✅ Verify Containers

```bash
docker ps
```

Expected:

* `zookeeper` → Port `2181`
* `kafka` → Port `9092`

---

# 🚀 Kafka CLI Commands

## 📖 Help Commands

```bash
docker exec -it kafka kafka-topics --help
docker exec -it kafka kafka-console-producer --help
docker exec -it kafka kafka-console-consumer --help
docker exec -it kafka kafka-consumer-groups --help
docker exec -it kafka kafka-configs --help
```

---

## 📝 Create Topic

```bash
docker exec -it kafka \
kafka-topics --create \
--topic my-topic \
--bootstrap-server localhost:9092 \
--partitions 1 \
--replication-factor 1
```

## 📋 List Topics

```bash
docker exec -it kafka \
kafka-topics --list \
--bootstrap-server localhost:9092
```

## 🔍 Describe Topic

```bash
docker exec -it kafka \
kafka-topics --describe \
--topic my-topic \
--bootstrap-server localhost:9092
```

## ✏️ Delete Topic

```bash
docker exec -it kafka \
kafka-topics --delete \
--topic my-topic \
--bootstrap-server localhost:9092
```

---

## ✍️ Start Producer

```bash
docker exec -it kafka \
kafka-console-producer \
--topic my-topic \
--bootstrap-server localhost:9092
```

Type messages and press Enter.

Example:

```text
Hello Kafka
Learning Kafka with Docker
```

---

## 📥 Start Consumer

```bash
docker exec -it kafka \
kafka-console-consumer \
--topic my-topic \
--from-beginning \
--bootstrap-server localhost:9092
```

---

## 👥 Consumer Groups

### List Consumer Groups

```bash
docker exec -it kafka \
kafka-consumer-groups \
--list \
--bootstrap-server localhost:9092
```

### Describe Consumer Group

```bash
docker exec -it kafka \
kafka-consumer-groups \
--describe \
--group my-group \
--bootstrap-server localhost:9092
```

---

## ⚙️ Broker Configurations

```bash
docker exec -it kafka \
kafka-configs --help
```

---

## 🧹 Stop Containers

```bash
docker stop kafka zookeeper
```

## ❌ Remove Containers

```bash
docker rm -f kafka zookeeper
```

## ❌ Remove Network

```bash
docker network rm kafka-net
```

---

## 📌 Quick Reference

| Operation               | Command                            |
| ----------------------- | ---------------------------------- |
| Create Topic            | `kafka-topics --create`            |
| List Topics             | `kafka-topics --list`              |
| Describe Topic          | `kafka-topics --describe`          |
| Delete Topic            | `kafka-topics --delete`            |
| Start Producer          | `kafka-console-producer`           |
| Start Consumer          | `kafka-console-consumer`           |
| List Consumer Groups    | `kafka-consumer-groups --list`     |
| Describe Consumer Group | `kafka-consumer-groups --describe` |
| Broker Configurations   | `kafka-configs --help`             |
