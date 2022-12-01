---
title: PER implementation with Caffeine for cache stampede problem
---

When we use cache, usually we set ttl (time-to-leave) per each cache key to refresh data. 
However, under heavy traffic environment this might cause an unexpected problem, known as "Cache Stemped Problem". <br>

Consider the case when one cache key has been expired, and multiple requests which requires the cache data have been arrived to the server.  <br>
Let's say the number of requests is N. In the worst case, it will request for original data for n times simultaneously. <br>
As the result, the graph for original data response count shows:

![]({{ 'images/스크린샷 2021-01-03 오후 8.58.43.png' | relative_url }})

There are multiple ways to solve this problem. First, we can utilize debouncing. Debouncing is to call the last request called in certain interval.
That is, if the interval set to 100ms and 5 requests arrived in 0ms ~ 100ms, first 4 requests will be thrown out and only the last one will be send to the original data server. <br>

Another thing is Probabilistic Early Recomputation, PER. It is to refresh the cache data before the cache key is actually expired for a certain probability. That is, it is to lower the spike in the graph and distribute the request to another time slot:

![]({{ 'images/스크린샷 2021-01-03 오후 9.38.58.png' | relative_url }})


# Implementation
I implemented the perGet method which returns the cached data with exponentially distributed probability, with caffeine cache.
<br>

First thing I needed to do is to calculate the time left before the expiration: 
```kotlin
fun <K, V> Cache<K, V>.getExpirationDelayMillis(k: K): Long {
    var res = Long.MAX_VALUE
    policy().expireAfterWrite().ifPresent {
        res = min(res, it.expiresAfter.toMillis() - it.ageOf(k).get().toMillis())
    }
    policy().expireAfterAccess().ifPresent {
        res = min(res, it.expiresAfter.toMillis() - it.ageOf(k).get().toMillis())
    }
    policy().expireVariably().ifPresent {
        res = min(res, it.getExpiresAfter(k).get().toMillis())
    }
    return res
}
```
In this code, I created the extension for caffeine cache, and utilized the policy() method which contains the age for each key and expiry. As there are three policies for cache expiry in caffeine, I took the minimum value of each cache expiration time. <br>
If there is no expiry policy set, 

Next thing I needed to do is to decide whether cache will be expired or not.
I use the exponentially distributed expiration function, elaborated in [this paper](http://cseweb.ucsd.edu/~avattani/papers/cache_stampede.pdf):

```kotlin
val x = random.nextFloat().coerceIn(EXPIRATION_LOG_X_MIN, EXPIRATION_LOG_X_MAX)
if (expirationDelayMillis < EXPIRATION_COEFFICIENT * -ln(x)) {
    return null
}
    return it
```
# Utilization
I also created the reactive cache wrapper and test code to show how it works.
For the reactive cache wrapper:
```kotlin
fun get(key: K, func: () -> Mono<V>): Mono<V> {
    val lookupMono = Function<K, Mono<Signal<out V>>> { k ->
        Mono.justOrEmpty(cache.perGet(k))
                .map { Signal.next(it) }
    }

    return CacheMono
            .lookup(lookupMono, key)
            .onCacheMissResume(Mono.defer(func))
            .andWriteWith { k, signal ->
                Mono.fromRunnable<Void> {
                    if (signal.isOnError) {
                        return@fromRunnable
                    }
                    signal.get()?.let { cache.put(k, it) }
                }
            }
}
```
In this code, lookupMono calls for the perGet I described above. If perGet returns null, "onCacheMissResume" will be called and update the cache with original data.


For the test code:

```kotlin
val cache: Cache<String, String> = Caffeine.newBuilder()
        .expireAfterWrite(Duration.ofSeconds(10))
        .build<String, String>()

val totalCount = AtomicInteger()
val missCount = AtomicInteger()
val reactivePerCache = ReactiveCaffeinePerCache(cache)
				
// first put 1000 entry into the cache
for (x in 1..1000) {
    reactivePerCache.get(x.toString()) {
       Mono.just("$x:v")
    }.subscribe()
}

// wait for a moment
Thread.sleep(8000L)

// then check cache hit
for (x in 1..1000) {
    reactivePerCache.get(x.toString()) {
        missCount.incrementAndGet()
        Mono.just("$x:v")
    }.subscribe()
    totalCount.incrementAndGet()
}
```
I defined the reactive cache with 10 seconds of write expiration setting.
Then I put the 1000 numbers in the cache and wait for 8 seconds, and check how many cache entries have been missed.  <br>
You can see that the miss rate increases exponentially as you increase the wait seconds.
# Code
You can find the entire source code in here: [https://github.com/wondershineone/cache-stampede-mitigation-impl](https://github.com/wondershineone/cache-stampede-mitigation-impl) <br>
# References
[https://en.wikipedia.org/wiki/Cache_stampede](https://en.wikipedia.org/wiki/Cache_stampede)
[https://meetup.toast.com/posts/251](https://meetup.toast.com/posts/251)
[http://cseweb.ucsd.edu/~avattani/papers/cache_stampede.pdf](http://cseweb.ucsd.edu/~avattani/papers/cache_stampede.pdf)
