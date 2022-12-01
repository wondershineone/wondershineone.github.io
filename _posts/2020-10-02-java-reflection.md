---
title: Java Reflection
tags:
- java
---

> re·flec·tion : the throwing back by a body or surface of light, heat, or sound without absorbing it.

**Java Reflection** : to get Class information with Object. <br>
Java class file is compiled into bytecode, placed in static area. <br> That is, if you know the class name you can access this area and get information of the class.

```
Class c = Data.class;

Method[] m = c.getMethods();                     
Field[] f = c.getFields();
Constructor[] cs = c.getConstructors();
Class[] inter = c.getInterfaces();
Class superClass = c.getSuperclass();
```
### Usages
#### 1. Invoke method with method name
```
Class class1 = Class.forName("name");
Method[] methods = class1.getMethods();
...
methods[0].invoke(methodObj, args);
```
#### 2. Create new object
```
Class class1 = Class.forName("name");
Constructor[] constructors = class1.getConstructors();
...
Object newObj = constructors[0].newInstance(arglist);
```
#### 3. Deserialization in ObjectInputStream.readObject()
#### 4. Apache Commons BeanUtils library
#### 5. Spring Dependency Injection
**Annotation is for adding metadata in code.**
With this, programmers can write some metadata at some point(Type, Method, Field,...) with certain retention (Source, Class, Runtime). <br>
**spring writes metadata of class on xml file and read it, and use reflection to utilize the class.** <br>

### Performance Issues
Btw, there are some reasons using reflection carefully, especially for the performance reasons:
1. The compiler can do no optimization whatsoever as it can have no real idea about what you are doing. This probably goes for the JIT as well.
2. Everything being invoked/created has to be discovered (i.e. classes looked up by name, methods looked at for matches etc)
3. Arguments need to be dressed up via boxing/unboxing, packing into arrays, Exceptions wrapped in InvocationTargetExceptions and re-thrown etc.
4. All the processing that Jon Skeet mentions here: <br>
1) Check that there's a parameterless constructor <br>
2) Check the accessibility of the parameterless constructor <br>
3) Check that the caller has access to use reflection at all <br>
4) Work out (at execution time) how much space needs to be allocated <br>
5) Call into the constructor code (because it won't know beforehand that the constructor is empty)


(from : [https://stackoverflow.com/questions/1392351/java-reflection-why-is-it-so-slow](https://stackoverflow.com/questions/1392351/java-reflection-why-is-it-so-slow)

## References
[https://gyrfalcon.tistory.com/entry/Java-Reflection](https://gyrfalcon.tistory.com/entry/Java-Reflection)
[https://loco-motive.tistory.com/31](https://loco-motive.tistory.com/31)
[https://woovictory.github.io/2019/01/04/Java-What-is-Reflection/](https://woovictory.github.io/2019/01/04/Java-What-is-Reflection/)
[https://stackoverflow.com/questions/1392351/java-reflection-why-is-it-so-slow](https://stackoverflow.com/questions/1392351/java-reflection-why-is-it-so-slow)
