---
title: "[Kafka] Notes"
tags:
- kafka
---

- Kafka is open source streaming-processing software platform.
- When producer produces messages to target topic on broker (to specific partition), each consumer in consumer group subscribing that topic consumes message from the topic (from specific partition).
- Data is not removed after one consumer group consumes. Each consumer group has its consumer offsets marking its latest processed message.
- Although kafka saves messages on disk, as kafka utilizes OS page cache we can use kafka as fast as in-memory queue.
- As OS page cache utilizes the remaining memory allocated to the application as a cache, the memory should be left on the machine to be utilized as a page cache. Recommended jvm server memory option is : < Xmx5G, < Xms5G (which can be differed from the machine)
- Since Kafka delegates cache processing to OS without performing separate cache processing, it can be utilized instantly without having to warm up the cache even if the Kafka process goes down and back to online.
- Because the message is not stored in memory, it is possible to prevent the message from growing in size as it is converted into a JVM object and to avoid performance degradation due to the GC of the JVM.
- Kafka's client basically support batch processing. That is, it allows multiple records to be grouped into one large request and delivered to the cluster.
- As message can be consumed from each partition one at a time, it is useless to increase the number of consumers in one consumer group by more than the number of partitions of the corresponding topic.
