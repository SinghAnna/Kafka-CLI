# 📬 Kafka Message Ordering

## What is Message Ordering?

**Message Ordering** means messages are consumed in the same order in which they were produced.

Example:

```text
Producer sends:

Order-1
Order-2
Order-3
Order-4
```

Consumer receives:

```text
Order-1
Order-2
Order-3
Order-4
```

The order is preserved.

---

# ✅ Ordering Guarantee in Kafka

Kafka guarantees ordering **only within a single partition**.

Example:

```text
Topic : orders

Partition-0

Offset : 0        1        2        3
Message: Order-1  Order-2  Order-3  Order-4
```

Consumer reads:

```text
Order-1
Order-2
Order-3
Order-4
```

The order is guaranteed because all messages are stored in the same partition.

---

# ❌ Ordering Across Multiple Partitions

Suppose we have:

```text
Topic : orders

Partition-0
Offset : 0     1
Message: A     C

Partition-1
Offset : 0     1
Message: B     D
```

Producer sends:

```text
A
B
C
D
```

Consumer may receive:

```text
A
C
B
D
```

or

```text
B
A
D
C
```

Kafka does not guarantee ordering across partitions.

Ordering is guaranteed only inside each partition.

---

# Why Does Ordering Break Across Partitions?

Partitions are processed independently.

```text
Producer
   |
   v
Topic : orders
   |
   ├── Partition-0
   └── Partition-1
```

Different partitions can be read by different consumers simultaneously.

This increases throughput but global ordering is lost.

---

# Single Partition Example

```text
Producer
   |
   v
Topic
   |
   └── Partition-0

Messages:
M1 → M2 → M3 → M4
```

Consumer receives:

```text
M1 → M2 → M3 → M4
```

Ordering is preserved.

---

# Multiple Partition Example

```text
Topic : payments

Partition-0 : P1  P3  P5
Partition-1 : P2  P4  P6
```

Consumers:

```text
Consumer-1 -> Partition-0
Consumer-2 -> Partition-1
```

Processing may happen like:

```text
P1
P2
P4
P3
P5
P6
```

Global ordering is not guaranteed.

---

# How to Preserve Ordering?

## Option 1: Use One Partition

```bash
docker exec -it kafka kafka-topics \
--create \
--topic orders \
--partitions 1 \
--replication-factor 1 \
--bootstrap-server localhost:9092
```

Pros:

* Ordering guaranteed.

Cons:

* No parallelism.
* Lower throughput.

---

## Option 2: Use Message Keys

Producer:

```text
101:Order Created
101:Payment Completed
101:Order Delivered
```

Kafka hashes the key:

```text
Key = 101
```

All messages with the same key go to the same partition.

Example:

```text
Partition-0

Offset : 0          1                 2
Message:
101:Created
101:Payment
101:Delivered
```

Ordering for user `101` is preserved.

---

# Producer Example with Keys

```bash
docker exec -it kafka \
kafka-console-producer \
--topic orders \
--bootstrap-server localhost:9092 \
--property parse.key=true \
--property key.separator=:
```

Example messages:

```text
101:Created
101:Payment
101:Delivered

102:Created
102:Payment
102:Delivered
```

Possible partition distribution:

```text
Partition-0
101:Created
101:Payment
101:Delivered

Partition-1
102:Created
102:Payment
102:Delivered
```

Ordering is preserved for each customer.

---

# Ordering and Consumer Groups

Topic:

```text
Partition-0 : A B C
Partition-1 : D E F
```

Consumer Group:

```text
Consumer-1 -> Partition-0
Consumer-2 -> Partition-1
```

Consumer-1 always receives:

```text
A → B → C
```

Consumer-2 always receives:

```text
D → E → F
```

Ordering is preserved inside each partition.

---

# Real-World Example

## E-Commerce Orders

Wrong ordering:

```text
Order Delivered
Payment Completed
Order Created
```

This would break the system.

Correct approach:

Use:

```text
Key = OrderId
```

Example:

```text
1001:Order Created
1001:Payment Completed
1001:Order Delivered
```

All events go to the same partition and ordering remains correct.

---

# Interview Questions

### Does Kafka guarantee message ordering?

Yes, but only within a partition.

### Does Kafka guarantee ordering across partitions?

No.

### How do we preserve ordering?

* Use a single partition.
* Use message keys.

### Why are multiple partitions used if ordering breaks?

Because partitions provide:

* Parallel processing
* High throughput
* Scalability

### Is ordering preserved inside a consumer group?

Yes, for each partition.

---

# Important Points

* Ordering is guaranteed only within a partition.
* Ordering is not guaranteed across partitions.
* Messages with the same key are usually written to the same partition.
* Consumer groups preserve ordering per partition.
* More partitions increase throughput but may sacrifice global ordering.
