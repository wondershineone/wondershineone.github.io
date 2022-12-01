---
title: Spring Cloud Config setup with Github
---

# Config Server
Create the git repository, and add configuration file. <br>
Note that filename should be {serviceName}-{phase}.yaml if you want to use it with spring-cloud-config-client.
```yaml
// myservice-dev.yaml
foo:
    bar: false
```

Then, create spring config server project with the dependency:
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

Then you can see the property with {serverUrl}/{serviceName}/{phase}:
```json
{
    "name":"myservice",
    "profiles";["dev"],
    "label": null,
    "version": "${HEAD checksum}",
    "state": null,
    "propertySources": {
        "name":"${github file url}",
        "source": {
            "foo.bar":false
        }
    }
}
```
Note that version field contains git repository's HEAD checksum, and state is null.
<hr>
# Config Client
There are two ways to get config value from server. One is to implement fetcher which calls config server periodically from the bottom up.
The other one is to use spring-cloud-config-client. <br> 
Spring-cloud-config-client automatically detects the file with the name {serviceName}-{phase}.yaml and map each value to property, so we can easily map each configuration with @Value annotation. I'll give you the details about second method.
## Usage
First of all add spring-cloud-config-client dependency. For maven:
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```
and then you can use the property with @Value annotation.
```java
@Value("${foo.bar:true}")
private boolean fooBar;
```
## Refresh
When you use git as config server storage, you cannot utilize existing auto-refresh feature : [ConfigClientWatch](https://github.com/spring-cloud/spring-cloud-config/blob/master/spring-cloud-config-client/src/main/java/org/springframework/cloud/config/client/ConfigClientWatch.java) for now. <br>

In ConfigClientWatch, it watches  the property "config.client.state", and if it is changed, it refreshes the exisiting properties, and it works well using hashicorp vault as a storage. <br>
However, when you use git as a backend, "config.client.state" will always have null value, so refresh will not be triggered. <br>

It seems that there are two major ways to support refresh feature with git storage:
1. using @RefreshScope with actuator
2. using Event Bus

For the first method, additional setup is required to refresh periodically. Also, it always refresh the beans even when update is not required. <br>
For the second method, additional setup is required like rabbitmq. <br>

I found another way in [this blog](https://dlsrb6342.github.io/2019/06/28/spring-cloud-config-Auto-Refresh/).<br>
It is almost same with the existing [ConfigClientWatch](https://github.com/spring-cloud/spring-cloud-config/blob/master/spring-cloud-config-client/src/main/java/org/springframework/cloud/config/client/ConfigClientWatch.java), but instead of using "config.client.state", it uses "config.client.version", which contains git repository's HEAD checksum. <br> That is, when something is committed to repository, it detects the difference and refreshes properties. 

<hr>
# References
[https://dlsrb6342.github.io/2019/06/28/spring-cloud-config-Auto-Refresh/](https://dlsrb6342.github.io/2019/06/28/spring-cloud-config-Auto-Refresh/)
[https://medium.com/@yongkyu.jang/spring-cloud-config-server-f1e390f18cfc](https://medium.com/@yongkyu.jang/spring-cloud-config-server-f1e390f18cfc)
