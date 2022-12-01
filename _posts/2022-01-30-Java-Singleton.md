---
title: Java Singleton
tags:
- java
- singleton
---

Several ways to implement singleton in Java.
## 1. Eager Initialization
```
public class Foo {
private static Foo instance = new Foo(); 
    private Foo() {}
public static Foo getInstance() {
        return instance;
    }
}
```
or
```
public class Foo {
private static Foo instance;
    private Foo() {}
static {
        try {
            instance = new Foo();
	} catch (Exception e) {}
    }
    public static Foo getInstance() {
        return instance;
    }
}
```

1. Forbid to declare instance from outside, with declaring constuctor and member variable as private.
2. With declaring instance as static (or declaring in static block), it guarantees thread safety, as instance is created on class load.

```
// Classloader.java 1.8
protected Class<?> loadClass(String name, boolean resolve)
    throws ClassNotFoundException
{
    synchronized (getClassLoadingLock(name)) {
        // First, check if the class has already been loaded
        Class<?> c = findLoadedClass(name);
        if (c == null) {
            long t0 = System.nanoTime();
...
protected Object getClassLoadingLock(String className) {
    Object lock = this;
    if (parallelLockMap != null) {
        Object newLock = new Object();
        lock = parallelLockMap.putIfAbsent(className, newLock);
        if (lock == null) {
            lock = newLock;
        }
    }
    return lock;
}
```

As you can see above, java classloader locks class with it's name,  That is, it guarantees thread safety if instance is created on class load.

Disadvantage of this trick is that it creates instance on class loading.
That is, more singleton class is created, more instance will be allocated (even before it used).

<br /> 

## 2. Lazy Initialization
```
public class Foo {
	
    private static Foo instance;
    private Foo () {}
	
    public static synchronized Foo getInstance() {
        if (instance == null) {
            instance = new Foo();
        }
        return instance;
    }
}
```
or
```
public class Foo {
	
    private static Foo instance;
    private Foo () {}
	
    public static Foo getInstance() {
        synchronized(Foo.class) {
            if (instance == null) {
                instance = new Foo();
            }
            return instance;
        }
    }
}
```

1.  Forbid to declare instance from outside, with declaring constuctor and member variable as private.
2.  It creates instance on getInstance() call, therefore it resolves "Class loadtime creation" on eager initialization.
3.  It guarantees thread safety with synchronized block.

```
// 1.8 Object.java
public class Object {
    ...
public final native void notify();
    public final native void notifyAll();
    public final void wait() throws InterruptedException {
        wait(0);
    }
}
```

Every object in Java has monitor lock.
When a thread access to static synchronized method (or block), it requires class lock.
Therefore, synchronized block above guarantees thread safety.

Disadvantage of this trick is that it requires class lock everytime we call getInstance().

<br /> 
## 3. Doubly Checked Locking
```
public class Foo {
	
    private volatile static Foo instance;
    private Foo () {}
	
    public static Foo getInstance() {
        if (instance == null) {
            synchronized(Singleton.class) {
                if (instance == null) {
                    instance == new Singleton();
                }
            }
        }
        return instance;
    }
}
```

This trick resolves synchronized call issue in lazy initialization.
It execute synchronized block on initial instance allocation, but once it is allocated it does not run synchronized block.
Plus, it prevents code rearrangement with volatile keyword.
reference : https://stackoverflow.com/questions/7855700/why-is-volatile-used-in-double-checked-locking  
<br /> 
## 4. Enum
```
public enum Foo {
    INSTANCE;
}
```

Enum guarantees compile-time initialization, therefore it guarantees single instance creation.
(But it does not guarantees method's thread safety)
Simplest, but as it initialized on compile time it has limited use. 

<br /> 
## 5. LazyHolder
```
public class Foo {
    private Foo() {}
    private static class FooHolder {
        private static final Foo INSTANCE = new Foo();
    }
    public static Foo getInstance() {
        return FooHolder.INSTANCE;
    }
}
```

1. Forbid to declare instance from outside, with declaring constuctor and member variable as private.
2. As there is no FooHolder instance in Foo, FooHolder is not initialized on Foo's load time. With calling getInstance(), classloader loads FooHolder, and it guarantees thread safety (like  eager initialization)

In short, it resolves  "Class loadtime creation" on eager initialization with FooHolder, and guarantees thread safety.

<br /> 
## References
http://happinessoncode.com/2017/10/04/java-intrinsic-lock/
https://www.jpstory.net/2015/03/02/mutex-semaphore-monitor/
https://kiwi99.tistory.com/19
https://blog.seotory.com/post/2016/03/java-singleton-pattern
https://medium.com/@joongwon/multi-thread-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C%EC%9D%98-%EC%98%AC%EB%B0%94%EB%A5%B8-singleton-578d9511fd42
https://limkydev.tistory.com/67
https://homoefficio.github.io/2018/10/13/Java-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%A1%9C%EB%8D%94-%ED%9B%91%EC%96%B4%EB%B3%B4%EA%B8%B0/
https://m.blog.naver.com/jjoommnn/130036635345
https://m.blog.naver.com/jjoommnn/130036640859
https://stackoverflow.com/questions/2531873/how-thread-safe-is-enum-in-java
