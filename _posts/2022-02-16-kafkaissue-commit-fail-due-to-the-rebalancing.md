---
title: "[Kafka][Issue] Commit fail due to the rebalancing"
tags:
- Kafka
- issue
---

## Problem
Consumer lag on some topic was increasing with the consumer failure occuring continuously.

## Assumption
Judging from the fact that lag is concentrated on a particular partition and the commit exception occurs about 10 minutes after the new partitions are assigned, including the particular partition on the log
We estimated that certain tasks in that partition were blocked and that the processing time exceeded max.poll.interval.ms (= 10 minutes) and rebalancing occurred, preventing the consumer from being attached.

## Solution
Found the part that can takes more than 10 minute in case of processing datas of some user.
Modified that part to be run on the background when the amount of data to be processed is too big.
