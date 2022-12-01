---
title: Setup switch deploy with Nginx
---

If you manages server group with baremetal machines and switches, 
deployment might become very tricky if switch does not support healthcheck for verifying if each host or instance is available (l7check). <br>

In my previous project, infra team did not support switch l7check. Instead, they provided admin to "manually exclude" each hosts on deployment.
It takes more than 10 minutes to completely exclude "each" machine from switch. <br>

So I came up with an idea utilizing nginx config reload.
If nginx config reload process works gracefully, it would switches upstream port to newer instance on deployment:

![overview](/images/1_PpWR-HE--sXJG5Sm8sQ9jQ.jpeg)

<hr>
## Nginx hot reload without Downtime
First I checked if nginx config reload works gracefully.  <br>
In official document :  [http://nginx.org/en/docs/beginners_guide.html](http://nginx.org/en/docs/beginners_guide.html)

> Once the master process receives the signal to reload configuration,
> it checks the syntax validity of the new configuration file and tries to
> apply the configuration provided in it.
> If this is a success, the master process starts new worker processes 
> and sends messages to old worker processes, requesting them to shut down.
> Otherwise, the master process rolls back the changes and continues to work with the old configuration. 
> Old worker processes, receiving a command to shut down,
> stop accepting new connections and continue to service current requests 
> until all such requests are serviced. After that, the old worker processes exit.

That is,

1. If master receives reload signal, it verify new configs and adopt it.
2. If it does not work, it shows error message and do nothing.
3. If it works, master creates new worker processes and sends new requests to those. At the same time, master sends gracefull shutdown signal to old worker processes (to finish exisiting requests). 

<hr>
## Scenario
Port groups (api / monitoring / jmx) are controlled by single portindex. <br>
For example, if portindex is set to 0,  port group 1 will be used. <br>
If portindex is set to 1, port group 2 will be used.

1. First, check application status: <br>
1) Check if new ports (api / monitoring / jmx) available.<br> 
2) Check if new ports is not served via nginx upstream.<br>
2. Changes flag.
3. Starts new application with new ports, and wait until it application is up and returns success signal on warmup api call.
4. Rewrite nginx config to point new upstream, and run nginx reload. Wait until nginx config reload done and warmup api call **"VIA NGINX"** goes to new instance.
5. Stop deprecated instance.

<hr>
## Test Result
With 10 clients constantly requesting api, I deployed single machine with above scenario.

![test result](/images/1_rleI7A-NwqRVd2nyGdfZIQ.png)

In capture, logs in blue box shows that reload successfully worked between those. <br>
It seems that it took 14ms more than previous request, but it looks fine, I guess. <br>

All status is 200, both in server logs or on client side.

<hr>
## Feedbacks
Some of my friends gave me some feedback:

1. You should leave some extra space for 2 * instance (+ nginx and monitoring processes) to prevent memory swap on deployment.
2. It would be better to check nginx status with other monitoring tools, or always keep nginx alive with supervisor.

<hr>

## References
[http://nginx.org/en/docs/beginners_guide.html](http://nginx.org/en/docs/beginners_guide.html)

[https://serverfault.com/questions/378581/nginx-config-reload-without-downtime](https://serverfault.com/questions/378581/nginx-config-reload-without-downtime)

[https://code.luasoftware.com/tutorials/nginx/nginx-hot-reload-without-downtime/](https://code.luasoftware.com/tutorials/nginx/nginx-hot-reload-without-downtime/)

[https://www.howtogeekpro.com/10/how-to-gracefully-restart-nginx-and-why/](https://www.howtogeekpro.com/10/how-to-gracefully-restart-nginx-and-why/)
