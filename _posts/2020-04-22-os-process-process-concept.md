---
title: "[OS] Process - Process Concept"
tags:
- os
- process
---

# The Process

![]({{ 'images/image.png' | relative_url }})
<p class="ref-comment">From https://commons.wikimedia.org/wiki/File:Process-in-memory.jpg </p>

**Process = Program on execution** <br>
**Program : Passive entity**, with command lists on file <br>
**Process : Active entity,** with Program counter and corresponding resources <br>

<hr>
# Process State
![]({{ 'images/process-diagram.png' | relative_url }})
<p class="ref-comment">From https://www.d.umn.edu/~gshute/os/processes-and-threads.xhtml </p>


There are 5 steps for Process

**NEW**
: process is on creation

**RUNNING**
: process is executing commands

**WAITING**
: process is waiting for some signal or IO to be completed

**READY**
: process is waiting to be assigned to processor

**TERMINATED**
: process is terminating

To be more specific, considering swap space there would be 2 more states:
![]({{ 'images/image (1).png' | relative_url }})
<p class="ref-comment">From https://en.wikipedia.org/wiki/Process_state </p>


**SWAPPED OUT AND WAITING** 
: process has been swapped out on waiting

**SWAPPED OUT AND BLOCKED** 
: process has been swapped out on blocked

<hr>
# Process Control Block
![]({{ 'images/image (2).png' | relative_url }})
<p class="ref-comment">From https://cssimplified.wordpress.com/2015/11/23/process-control-block-pcb/ </p>


PCB is data structure in the operating system kernel containing the information needed to manage a particular process. <br>
The fields varies to OS, but there are some common fields like: <br>

**Process Status** <br>
: new, ready, running, waiting, etc. <br>

**Program Counter (PC)** <br>
: PC points next command of the process to be executed. <br>

**CPU Registers** <br>
: accumulator, index register, stack register, general purpose register, etc. These states are saved with PC on interrupt, and then restored on execution. <br>

**CPU Scheduling Infos**<br>
: process priority, pointer to schedule queue and other scheduling variables. <br>

**Memory Management Infos**<br>
: base register, limit register, page table, segment table, etc. <br>

**Accounting Infos** <br>
: CPU use time, actual use time, time limit, account number, job or process number, etc. <br>

**IO status Infos** <br>
: IO Devices on this process, opened file list, etc.

<hr>

# References
<br>

[https://commons.wikimedia.org/wiki/File:Process-in-memory.jpg](https://commons.wikimedia.org/wiki/File:Process-in-memory.jpg)

[http://www.d.umn.edu/~gshute/os/processes-and-threads.xhtml](http://www.d.umn.edu/~gshute/os/processes-and-threads.xhtml)

[https://stackoverflow.com/questions/28795024/who-controls-the-process-control-blockpcb/28795091](https://stackoverflow.com/questions/28795024/who-controls-the-process-control-blockpcb/28795091)

[https://cssimplified.wordpress.com/2015/11/23/process-control-block-pcb/](https://cssimplified.wordpress.com/2015/11/23/process-control-block-pcb/)
