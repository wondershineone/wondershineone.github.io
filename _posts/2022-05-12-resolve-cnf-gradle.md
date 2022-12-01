---
title: Resolve ClassNotFoundException on Gradle build
tags:
- java
- gradle
---

This article is about resolving java.lang.ClassNotFoundException on runtime, with gradle build. <br/>

# Cause
There are various reasons causing java.lang.ClassNotFoundException on runtime. <br/>
Basically when java program is executed, it finds necessary classes in classpath. If the program uses various external libraries, their path also included in classpath. <br/>
If the app throws java.lang.ClassNotFoundException on runtime, it means that it cannot find the class in jars in classpath. <br/>
It can be caused by IDE setting issue, especially when you use multi module. For example, some sub-components not compiled but since main class knows the components it can be compiled successfully.

# Solution
First solution is to resolve dependency issue with several modules. However, if it is not possible or the issue is not clear, we can forcibly put corresponding jars on build. <br/>
Let's say you want to build jars with gradle and you want some library to be included in your output jar.  <br/>
By adding special tag on configurations for the dependencies to be included in jar, and by adding configuration in jar task (as suggested in [here](https://discuss.gradle.org/t/how-to-include-dependencies-in-jar/19571/4)), we can forcibly include those libs in output jar: <br/>

```gradle
// build.gradle

...

configurations {
    extraLibs
}

dependencies {
    extraLibs group: "...", name: "...", version: "..."
    configurations.compile.extendsFrom(configurations.extraLibs)
}

...

jar {
  from {
    configurations.extraLibs.collect { it.isDirectory() ? it : zipTree(it) }
  }
}

```

# References
[https://discuss.gradle.org/t/how-to-include-dependencies-in-jar/19571/4](https://discuss.gradle.org/t/how-to-include-dependencies-in-jar/19571/4)
[https://codechacha.com/ko/java-classnotfoundexception/](https://codechacha.com/ko/java-classnotfoundexception/)
