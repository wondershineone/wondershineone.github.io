---
title: "[OS] Thread - Threading Issues"
---

# Fork() and Exec() system call
fork() call is for process making its child process.
exec() call replaces process including threads with another.

#### Q. When fork() call, should we copy all threads or creates one new thread would be enough ?
If process immediately calls exec() after fork() call, copying all threads is unnecessary.
If process does not calls exec(), copying all threads might necessary.

<br/>

# Cancellation
Thread cancellation means force terminating before thread finishes it's job. 
For example, if one thread searching for a data found its result, other threads should be stop.
Thread cancellation can be done in two ways:

1. **Asynchronous Cancellation** :  One threads immediately terminates target thread
2. **Deferred Cancellation** : Target thread periodically checks if it should be terminated.

Handling resources allocated with target thread makes it more difficult. Sometimes, OS does not collect all resources from cancelled thread.  

On Deferred cancellation, one thread marks target thread,
and when target thread is safe to terminate, it can check if there is cancel mark.

Pthreads call this "safe point" as cancellation point.

Java supports interrupt() call for thread deferred cancellation.

1. one thread can mark another thread with **interrupt()** method
2. threads checks for its mark with **interrupted()** or **isInterrupted()** call

For example:

```java
Thread thrd = new Thread(new InterruptibleThread());
thrd.start();
...
thrd.interrupt();
...

class InterruptibleThread implements Runnable {
    public void run() {
        while (true) {
            ...
            if (Thread.currentThread().isInterrupted()) {
                 break;
             }
        }
    // cleanup / terminate
    }
 }
 ```
<br/><br/>

 
# Signal Handling
Signal is used for letting process know something happened on UNIX. Signal can be processed asynchronous / synchronous.
1. Some event occurs, then signal is created
2. Once signal is created, it is sent to process
3. When signal is sent, it must be processed

Synchronous signal includes: Illegal memory access, Divide by 0 <br/>
All Signals handled by one of the:
1. Default Signal Handler (by kernel)
2. User defined Signal Handler
<br/><br/>

# LWP
![](/images/20220428_image_2.png)

In Many-to-Many model, there are user thread <-> kernel thread communication issue. <br/>
To increase communication performance between user thread <-> kernel thread, many systems supports **Light Weight Process**. 

The difference between thread / LWP is that:
**LWP** : Process managed by PCB
**Thread** : can be LWP or another thread object depends on os
<br/><br/>

# Thread Pooling 
pooling thread can prevent system from taking too much resources like cpu / memory
<br/><br/>

# Thread Local
In java, thread can have its own datas with thread local
<br/><br/>

# ETC
Task is default unit for processor / kernel scheduling.
In unix, process or thread can be task <br/>

PCB(Process Control Block) is for process
TCB(Task Control Block) is for thread
<br/><br/>

# References
- [https://www.os-book.com/OS9/java-dir/4.pdf](https://www.os-book.com/OS9/java-dir/4.pdf)
- [http://www.iamroot.org/xe/index.php?mid=Programming&page=10&document_srl=14173](http://www.iamroot.org/xe/index.php?mid=Programming&page=10&document_srl=14173)
- [https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/4_Threads.html](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/4_Threads.html)
