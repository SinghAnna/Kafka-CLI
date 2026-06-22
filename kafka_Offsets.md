# 📊 Kafka Offsets

## What is an Offset?

An **offset** is a sequential number assigned to every message inside a Kafka partition. It represents the position of a record in that partition.

### Example

```text
Topic : orders
Partition-0

Offset : 0     1     2     3     4
Message: A     B     C     D     E
```

| Message | Offset |
| ------- | ------ |
| A       | 0      |
| B       | 1      |
| C       | 2      |
| D       | 3      |
| E       | 4      |

---

## Why Do We Need Offsets?

Kafka uses offsets to track:

* Which messages have already been consumed.
* From where a consumer should resume after a restart.
* Consumer progress and lag.

Example:

```text
Offset : 0     1     2     3     4
Message: A     B     C     D     E
                        ^
                 Consumer Position
```

The consumer has already processed:

```text
A → B → C
```

The next message to be read is:

```text
D
```

---

## Consumer Restart Example

```text
Offset : 0     1     2     3     4
Message: A     B     C     D     E
                              ^
                      Current Offset = 4
```

Suppose the consumer crashes.

After restarting:

```bash
docker exec -it kafka kafka-console-consumer \
--topic orders \
--group order-group \
--bootstrap-server localhost:9092
```

Kafka automatically resumes from the last committed offset instead of reading all messages again.

---

## Where Are Offsets Stored?

Kafka stores consumer offsets in an internal topic:

```text
__consumer_offsets
```

This topic should generally not be deleted.

---

## View Consumer Offsets

```bash
docker exec -it kafka kafka-consumer-groups \
--describe \
--group order-group \
--bootstrap-server localhost:9092
```

Example output:

```text
TOPIC    PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG
orders   0          10              15              5
```

Meaning:

* Current Offset = 10
* Latest Offset = 15
* Lag = 5 messages

Formula:

```text
Lag = LOG-END-OFFSET - CURRENT-OFFSET
Lag = 15 - 10 = 5
```

---

## Reset Offset to Beginning

```bash
docker exec -it kafka kafka-consumer-groups \
--group order-group \
--topic orders \
--reset-offsets \
--to-earliest \
--execute \
--bootstrap-server localhost:9092
```

---

## Reset Offset to Latest

```bash
docker exec -it kafka kafka-consumer-groups \
--group order-group \
--topic orders \
--reset-offsets \
--to-latest \
--execute \
--bootstrap-server localhost:9092
```

---

## Reset to a Specific Offset

```bash
docker exec -it kafka kafka-consumer-groups \
--group order-group \
--topic orders \
--reset-offsets \
--to-offset 5 \
--execute \
--bootstrap-server localhost:9092
```

The consumer will start reading from:

```text
Offset = 5
```

---

## Real-World Example

```text
Partition-0

Offset : 0   1   2   3   4   5   6
Message: A   B   C   D   E   F   G
```

Consumer processed:

```text
A → B → C → D
```

Committed Offset:

```text
4
```

Application crashes.

After restart:

```text
Consumer resumes from Offset 4
```

Next messages:

```text
E → F → G
```

---

## Important Points

* Offsets are **partition-specific**.
* Offsets are **not unique across the entire topic**.
* Kafka uses offsets to track consumer progress.
* Consumer groups store offsets in `__consumer_offsets`.
* Offsets allow consumers to resume processing after failures.
* Consumer lag indicates how far behind a consumer is.

---

## Interview Definition

> An offset is the sequential position of a record inside a Kafka partition. Kafka uses offsets to keep track of consumed messages and determine where a consumer should resume reading.
