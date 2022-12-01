---
title: What happens when we go to a website - 2. Http Call (http1.1 vs http2)
tags:
- web
- http
- http2
---

Now browser requests for the webpage with given url.

# HTTP 1.1
with http1.1:

1. It creates connection and fetches html page.
2. With parsing down the html page, it consequently fetches necessary resources like css file or js file.

Problems with this approach are:

1. Opening and closing sockets at every request can result in wasted networks and unnecessary delays
2. Each request may include duplicate header values and may continue to send and receive unnecessary data.

The first one can be improved with keepalive and pipelining. However, Keepalive and Pipelining also have problems: <br>
1. If keepalive setting is incorrect, it may keep opening the connection and eating up the memory.
2. Pipelining will have to receive the results sequentially anyway, so if the previous requests are pushed back, next requests will be pushed back  too (HOL problem)

# HTTP 2
Http/2 improved the problems with:

1. multiplexed streams, stream prioritization, server push
2. header compression


## Multiplexed Stream
First of all, http2 divides the message (the unit of request/response in http1.1) with "frame". Also it defines the new concept "stream", which can contain multiple messages. <br>
In conclusion, multiple frames consist of one message, and multiple messages consist of one stream. <br>

With these concept:

1. Client opens stream and sends headers frame first. When client finished, it notifies the server with flag "END_HEADERS"
2. If client have some data to send, it sends data with data frames. When client finished, it notifies the server with flag "END_STREAM"
3. Server sends headers frame. When server finished, it notifies the client with flag "END_HEADERS"
4. If sever have some data to send, it sends data with data frames. When server finished, it notifies the client with flag "END_STREAM"

This solves HOL problem as frame doesn't have to be sequentially delivered. First frame first processed in the same stream.
<br>

## Stream Prioritization
When requesting a resource file such as css / image, it determines the priority between resources. <br>
For example, if the image is fetched before the css, the rendering process will be delayed, so it fetches css file before the images.
<br>

## Server Push
If a client requests an html page and the page contains multiple resource files, 
Server sends "PUSH_PROMISE" frame to the client, saying that "I'll send you these files later". <br>
When the client sends OK sign, the files will be delivered later. Client can also decline PUSH_PROMISE if resources are already cached in the class. <br>

## Header Compression
With static/dynamic header table, it finds duplicated headers from a client request. <br>
It sends only indices with duplicates. With non-duplicates, it huffman-encodes the headers. 
<br>
# References
[https://d2.naver.com/helloworld/59361](https://d2.naver.com/helloworld/59361)
[https://b.luavis.kr/http2/http2-overall-operation](https://b.luavis.kr/http2/http2-overall-operation)
[http://americanopeople.tistory.com/115](http://americanopeople.tistory.com/115)
[https://www.netmanias.com/ko/post/blog/5348/dhcp-ip-allocation-network-protocol/understanding-the-basic-operations-of-dhcp](https://www.netmanias.com/ko/post/blog/5348/dhcp-ip-allocation-network-protocol/understanding-the-basic-operations-of-dhcp)
[https://www.slideshare.net/JinKyoungHeo/1-2-53043752](https://www.slideshare.net/JinKyoungHeo/1-2-53043752)
[https://poiemaweb.com/js-dom](https://poiemaweb.com/js-dom)
[https://www.popit.kr/%EB%82%98%EB%A7%8C-%EB%AA%A8%EB%A5%B4%EA%B3%A0-%EC%9E%88%EB%8D%98-http2/](https://www.popit.kr/%EB%82%98%EB%A7%8C-%EB%AA%A8%EB%A5%B4%EA%B3%A0-%EC%9E%88%EB%8D%98-http2/)
