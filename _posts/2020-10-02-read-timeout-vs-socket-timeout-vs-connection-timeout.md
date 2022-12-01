---
title: Read Timeout vs Socket Timeout vs Connection Timeout
tags: []
---

### Socket Timeout
Timeout on each packet.
### Read Timeout
Read timeout is similar to socket timeout, but connection is already created and packet is not received.
### Connection Timeout
When TCP Connection is not created, that is, http 3-way handshake is not succeeded.

## References
[https://tomining.tistory.com/164](https://tomining.tistory.com/164)
