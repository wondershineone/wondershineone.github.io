---
title: CAP Theory
---

In distribution system, it is impossible to have all three features at the same time.

- Consistency
- Availability
- Partitions Tolerance

**Consistency**
: Every node must **show** same data at same time

**Availability**
: Even if specific node **fails**, service must be served

**Partitions Tolerance**
: Even if specific node **cannot reach another node**, service must be served

<hr>

## CA
**pros**
: show same data at same time, even if one node fails, service can be served <br>

**cons**
: If specific node cannot reach another node, service cannot be served <br>

- **Relational** : RDBMS (mysql, postgres, etc)
- **Column Oriented** : Vertica

<hr>

## CP
**pros**
: show same data at same time, event if specific node cannot reach another node, service can be served <br>

**cons**
:  If one node fails, service cannot be served <br>

- **Column Oriented** : HBase, BigTable
- **Document Oriented** : MongoDB
- **Key-Value** : Redis, Memcached

<hr>

## AP
**pros**
: event if specific node cannot reach another node or dies, service can be served <br>

**cons**
:  Might not show same data at the same time <br>

- **Column Oriented** : Cassandra
- **Document Oriented** : CouchDB

<hr>
