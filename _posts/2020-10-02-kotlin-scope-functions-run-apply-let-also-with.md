---
title: Kotlin Scope Functions (run, apply, let, also, with)
tags:
- kotlin
---

## run
```kotlin
inline fun <T, R> T.run(block: T.() -> R): R {
    return block()
}

// usage
val item : R = T().run {
   val myDao = getMyDao();
   return@run myDao.insert(Row(this.name));
} 
```
- to calculate some value
- to limit multiple local variables' scope
<hr>
## apply
```kotlin
inline fun <T> T.apply(block: T.() -> Unit): T {
    block()
    return this
}

// usage
val item : T = T().apply { 
     this.age = 16
     this.name = "name"
}
```
- to change property without using its function
<hr>
## let
```kotlin
inline fun <T, R> T.let(block: (T) -> R): R {
    return block(this)
}

// usage
val item : R = T()?.let {
    dao.insert(Row(it.name));
    return@let it.toR();
}
```
- to run code when object is not null
- changing nullable object to another nullable object
- limit local variable scope
<hr>
## also
```kotlin
inline fun <T> T.also(block: (T) -> Unit): T {
    block(this)
    return this
}

// usage
val item : T = T().also {
    requireNotNull(it.age)
}
```
- takes effect not using "this" function or changing "this" property.
- "this" property sanity check, logging, etc
<hr>
## with
```kotlin
inline fun <T, R> with(receiver: T, block: T.() -> R): R {
    return receiver.block()
}

// usage
val item : R = with(T()) { 
    print(this.name)
    print(this.age)
}
```
- when object is not nullable
- when result of block is not necessary
<hr>
## References
[https://medium.com/@limgyumin/%EC%BD%94%ED%8B%80%EB%A6%B0-%EC%9D%98-apply-with-let-also-run-%EC%9D%80-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94%EA%B0%80-4a517292df29](https://medium.com/@limgyumin/%EC%BD%94%ED%8B%80%EB%A6%B0-%EC%9D%98-apply-with-let-also-run-%EC%9D%80-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94%EA%B0%80-4a517292df29)
[https://kotlinlang.org/docs/reference/scope-functions.html](https://kotlinlang.org/docs/reference/scope-functions.html)
