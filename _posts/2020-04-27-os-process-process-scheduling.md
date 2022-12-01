---
title: "[OS] Process - Process Scheduling"
tags:
- os
- process
---

# Scheduling Queues
![](/images/20220427_image.png)


When process enters system, PCB goes to Ready Queue first, and waits to be executed.
Once process occupy the processor, some events like:
- time slice expired
- process requests for I/O job
- process creates another subprocess and waits for it to terminate
After those events, process goes to ready queue again. Process repeats these over and over again. <br/>
When process has its job done (or terminated), PCB is deleted in every queues and deallocates its PCB and resources. <br/>

![](/images/20220427_image_1.png)

Each Queue's header has head / tail pointing its first / last PCB

<hr/>

# Scheduler
![](/images/20220427_image_2.png)

### Long Term Scheduler
Some system has limits for total process quantity on memory, and save processes which cannot be accepted in memory to disk. <br/>
Long term scheduler is for those processes, to move processes from disk to memory. <br/>
Note that some system does not have long term scheduler, or takes minimal role. For example, in LINUX or Windows there are no long term scheduler, they just put every new process in to memory. <br/>

### Short Term Scheduler
short term scheduler picks process and move process to processor
![](/images/20220427_image_3.png)

### Mid Term Scheduler
Some system adopted scheduling system so called mid term scheduler. <br/>
When there are no enough memory to contain all process, scheduler picks some processes and move it to disk(Swap Out). <br/>
When necessary, those process are moved from disk to memory (Swap In). <br/>
Context Switch
To allocate another process in processor,
it is necessary to save current status on PCB and load new process's PCB. This is so called Context Switch, which is pure overhead.

<hr/>

## References

- [https://blog.tsunanet.net/2010/11/how-long-does-it-take-to-make-context.html](https://blog.tsunanet.net/2010/11/how-long-does-it-take-to-make-context.html)
- [https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/3_Processes.html](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/3_Processes.html)
- [https://techdifferences.com/difference-between-long-term-and-short-term-scheduler.html](https://techdifferences.com/difference-between-long-term-and-short-term-scheduler.html)
- [https://blog.tsunanet.net/2010/11/how-long-does-it-take-to-make-context.html]( https://blog.tsunanet.net/2010/11/how-long-does-it-take-to-make-context.html)
