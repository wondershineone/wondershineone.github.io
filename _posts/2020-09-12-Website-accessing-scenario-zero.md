---
title: What happens when we go to a website - 0. Acquire my IP
tags:
- network
- web
---

"Host1" under "Router1" tries to acquire IP.

0. If Host1's ip is cached in local, Host1 use it.
1. If not, Host1 broadcasts discover packet to local network(hosts under Router1) with its mac address:
```
I'm Host1 and I want to find DHCP server
```
2. If any DHCP server under Router1 takes broadcast packet, it picks available ip in its ip pool, <br>
and broadcasts offer packet (if discover packet's broadcast flag is 1) <br>
or unicasts offer packet (if discover packet's broadcast flag is 0) to local network: <br>
```
I'm DHCP server "DHCP1" with ip address "dhcp_ip1", and I can give you an ip. Would you like to take it?
DNS address is "dns_addr1" and you can use this ip until ...
```
3. Host1 picks one dhcp server and sends request packet:
```
Hello DHCP1 I'd like to take the ip.
```
4. DHCP1 unicast or broadcast network information packt for Host1
5. If Host1 takes the packet, done. 

Note that hosts usually take primary / secondary DNS' information. If primary does not work, it uses secondary.

### References
[https://d2.naver.com/helloworld/59361](https://d2.naver.com/helloworld/59361)
[http://americanopeople.tistory.com/115](http://americanopeople.tistory.com/115)
[https://www.netmanias.com/ko/post/blog/5348/dhcp-ip-allocation-network-protocol/understanding-the-basic-operations-of-dhcp](https://www.netmanias.com/ko/post/blog/5348/dhcp-ip-allocation-network-protocol/understanding-the-basic-operations-of-dhcp)
