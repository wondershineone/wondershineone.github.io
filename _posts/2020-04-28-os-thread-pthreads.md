---
title: "[OS] Thread - PThreads"
tags:
- os
- thread
---

**PThreads** is Standard API for creating / synchronizing threads, made by POSIX. Each OS implements this API.
PThreads includes:
## pthread_create

```c
int pthread_create(pthread_t thread,
                   const pthread_attr_t attr,
                   void (start_routine)(void ), void arg);
```

Creates thread
- **thread** : unique thread ID
- **attr** : thread attributes. default = NULL
- **start_routine** : functions to be run on thread
- **arg** : argument passed to start_routine

<br/>

## pthread_self

```c
pthread_t pthread_self(void);
````
returns threads unique ID

<br/>

## pthread_join
```c
int pthread_join(pthread_t th, void **thread_return);
```

Wait thread stop, get thread exit signal and cleanup resources.
- **th** : thread ID to join
- **thread return** : thread return value

<br/>

## pthread_detach
```c
int pthread_detach(pthread_t th);
```

detaches thread from main thread using pthread_create.
separated thread immediately cleanup resources on exit, not waiting for pthread_join.

<br/>

## pthread_exit
```c
void pthread_exit(void *retval);
```
terminate current thread

<br/>

## pthread_mutex_init
```c
int pthread_mutex_init(pthread_mutex_t *mutex, const pthread_mutex_attr *attr);
```

inits thread mutex object
- **mutex** : target mutex object
- **attr** : mutex attributes (fast / recursive / error checking .. ). default = fast

<br/>

## pthread_mutex_lock
```c
int pthread_mutex_lock(pthread_mutex_t *mutex);
```
for thread mutex lock

<br/>

## pthread_mutex_unlock
```c
int pthread_mutex_unlock(pthread_mutex_t *mutex);
```
<br/>

## pthread_mutex_destroy
```c
int pthread_mutex_destroy(pthread_mutex_t *mutex);
```
<br/>

# References
- [https://blog.naver.com/myca11/221224081981](https://blog.naver.com/myca11/221224081981)
- [https://guswnsla1223.tistory.com/70](https://guswnsla1223.tistory.com/70)
