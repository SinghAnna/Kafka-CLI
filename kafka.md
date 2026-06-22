# Kafka Setup Using Docker

## Step 1: Create a Docker Network

```bash
docker network create kafka-net
```

## Step 2: Start Zookeeper

```bash
docker run -d \
  --name zookeeper \
  --network kafka-net \
  -p 2181:2181 \
  -e ZOOKEEPER_CLIENT_PORT=2181 \
  confluentinc/cp-zookeeper:7.4.0
```

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

## Verify Containers

```bash
docker ps
```

Expected output:

* `zookeeper` → Running on port `2181`
* `kafka` → Running on port `9092`
