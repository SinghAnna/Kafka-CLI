# 📦 Kafka Partitions and Replication Factor

## 📌 What is a Partition?

A **partition** is a subdivision of a Kafka topic. Messages inside a partition are stored in order and identified by offsets.

### Example

```text
Topic : orders

Partition-0 : A  B  C
Partition-1 : D  E  F
Partition-2 : G  H  I
```

A topic can have one or more partitions.

---

## Why Do We Need Partitions?

Partitions provide:

* Parallel processing
* Higher throughput
* Scalability
* Load distribution among consumers

Example:

```text
Producer
   |
   v
Topic : orders
   |
   ├── Partition-0 ───► Consumer-1
   ├── Partition-1 ───► Consumer-2
   └── Partition-2 ───► Consumer-3
```

Here, all three consumers can process messages simultaneously.

---

## Create a Topic with Partitions

```bash
docker exec -it kafka kafka-topics \
--create \
--topic orders \
--partitions 3 \
--replication-factor 1 \
--bootstrap-server localhost:9092
```

---

## Increase Partitions

```bash
docker exec -it kafka kafka-topics \
--alter \
--topic orders \
--partitions 5 \
--bootstrap-server localhost:9092
```

⚠️ Partitions can be increased but cannot be decreased.

---

## Offsets are Partition Specific

```text
Partition-0

Offset : 0   1   2
Message: A   B   C

Partition-1

Offset : 0   1   2
Message: D   E   F
```

Notice that both partitions start their offsets from `0`.

---

# 🔄 What is Replication Factor?

The replication factor specifies how many copies of each partition Kafka should maintain.

Example:

```text
Topic : orders
Replication Factor = 3

Broker-1 : Partition-0 (Leader)
Broker-2 : Partition-0 (Follower)
Broker-3 : Partition-0 (Follower)
```

There are three copies of the same partition.

---

## Why Do We Need Replication?

Replication provides:

* Fault tolerance
* High availability
* Data durability
* Recovery from broker failures

---

## Example

Suppose:

```text
Topic : orders
Partitions = 3
Replication Factor = 2
Brokers = 3
```

Distribution:

```text
Broker-1
 ├── Partition-0 (Leader)
 └── Partition-1 (Follower)

Broker-2
 ├── Partition-1 (Leader)
 └── Partition-2 (Follower)

Broker-3
 ├── Partition-2 (Leader)
 └── Partition-0 (Follower)
```

---

## What Happens if a Broker Fails?

Before failure:

```text
Broker-1 : Partition-0 (Leader)
Broker-2 : Partition-0 (Follower)
```

Broker-1 crashes:

```text
Broker-2 : Partition-0 (New Leader)
```

Kafka automatically elects a new leader.

Consumers continue reading messages without data loss.

---

## Create Topic with Replication

```bash
docker exec -it kafka kafka-topics \
--create \
--topic payments \
--partitions 3 \
--replication-factor 2 \
--bootstrap-server localhost:9092
```

⚠️ The replication factor cannot be greater than the number of brokers.

Example:

```text
Brokers = 2
Replication Factor = 3 ❌
```

```text
Brokers = 3
Replication Factor = 3 ✅
```

---

# 📌 Partition vs Replication Factor

| Feature                           | Partition   | Replication Factor |
| --------------------------------- | ----------- | ------------------ |
| Purpose                           | Parallelism | Fault Tolerance    |
| Increases Throughput              | ✅ Yes       | ❌ No               |
| Provides Backup                   | ❌ No        | ✅ Yes              |
| Helps Scaling                     | ✅ Yes       | ❌ No               |
| Helps Recover from Broker Failure | ❌ No        | ✅ Yes              |

---

# 🎯 Interview Questions

### Why do we use partitions?

To achieve parallel processing and increase throughput.

### Why do we use replication?

To prevent data loss and provide fault tolerance.

### Can partitions be decreased?

No.

### Can replication factor be greater than brokers?

No.

### Can replication improve performance?

No. Replication improves reliability, not throughput.

### Can a partition have multiple leaders?

No. A partition has only one leader at a time.
