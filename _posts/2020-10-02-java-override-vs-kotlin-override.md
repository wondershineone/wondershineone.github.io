---
title: Java @Override vs Kotlin override
tags:
- java
- kotlin
---

## Java
By default, if one class's function have
1. exactly same arguments
2. same or subtype return type
3. The access level is same or less restrictive
4. non-final method 
5. ...
6. same function name

it overrides parent's function. <br>
@Override enables subclass to override parent's method without 6) condition. <br>
@Override is nothing to do with bytecode. It's retention is source-code only. It's just a hint for compiler.

## Kotlin
Same rule for overriding with java, but the functions in Kotlin are defined as final, by default. That means you cannot override them. <br>
That is:
- java uses final to prevent override in inheritance. (default = enable)
- kotlin uses open to allow override in inheritance. (default = disable)

## References
[https://thdev.tech/kotlin/2016/10/09/Kotlin-Class/](https://thdev.tech/kotlin/2016/10/09/Kotlin-Class/)
[https://www.quora.com/Does-Override-have-any-effect-on-bytecode](https://www.quora.com/Does-Override-have-any-effect-on-bytecode)
[https://stackoverflow.com/questions/46399430/difference-between-open-and-override-methods-in-kotlin](https://stackoverflow.com/questions/46399430/difference-between-open-and-override-methods-in-kotlin)
[https://crunchify.com/java-method-overriding-examples-and-concepts-overriding-rules/](https://crunchify.com/java-method-overriding-examples-and-concepts-overriding-rules/)
[https://thdev.tech/kotlin/2016/10/09/Kotlin-Class/](https://thdev.tech/kotlin/2016/10/09/Kotlin-Class/)
