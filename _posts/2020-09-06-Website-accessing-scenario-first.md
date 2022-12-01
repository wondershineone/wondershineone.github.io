---
title: What happens when we go to a website - 1. Acquire website IP
tags:
- network
- web
---

First, browser must know actual ip address of domain. <br>
Let's say target domain is www.blabla.com.

1. If ip is cached in browser, simply connected to cached ip.
2. If not, browser makes a system call to the OS.
3. OS first look at it's local cache.  If ip is cached, OS simply returns cached ip to browser.
4. If domain does not exist, it asks root DNS. root DNS responds like:
```
Ask dns_foo which manages "com" domain
```
5. OS asks "dns_foo" again, then "dns_foo" responds like:
```
Ask dns_bar which manages "www.*.com" domain
```
6.  It goes until totally matched address is found.
7.  Then OS caches domain-ip address to local cache, then return it to browser.
8.  Browser caches domain-ip address if necessary.

### References
[https://d2.naver.com/helloworld/59361](https://d2.naver.com/helloworld/59361)
[https://b.luavis.kr/http2/http2-overall-operation](https://b.luavis.kr/http2/http2-overall-operation)
[http://americanopeople.tistory.com/115](http://americanopeople.tistory.com/115)
[https://www.netmanias.com/ko/post/blog/5348/dhcp-ip-allocation-network-protocol/understanding-the-basic-operations-of-dhcp](https://www.netmanias.com/ko/post/blog/5348/dhcp-ip-allocation-network-protocol/understanding-the-basic-operations-of-dhcp)
