---
title: "[OS] Process - Interprocess communication"
tags:
- os
- process
---

Processes which affects / be affected by other processes are called cooperating process. These processes take advantage of:

1. **Information Sharing** : Multiple user can access to same datas like sharing files.
2. **Computation speedup** : We can divide a task into subtasks and process those tasks in parallel.
3. **Modularity** : We may want to divide task into multiple process or threads for modularity.
4. **Convenience** : One user may perform multiple tasks like editing, printing, compiling at same time.

<img src="/images/20220427_image_6.png" style="width: 800px;">

Cooperating process needs **Interprocess Communication : IPC**
There are two models for this: 1. **Shared Memory**  2. **Message Passing**
<hr/>

# Shared Memory System
Other processes which wish to use the shared memory must then make their own system calls to attach the shared memory area onto their address space. 

### Datas on Shared Memory
```c
#define BUFFER_SIZE 10

typedef struct {
     . . .
} item;

item buffer[ BUFFER_SIZE ];
int in = 0;
int out = 0;
```

### Producer
```c
item nextProduced;
while( true ) {
      nextProduced = makeNewItem( . . . );
      
      // waits for space to be available
      while( ( ( in + 1 ) % BUFFER_SIZE ) == out ) {}
      
      buffer[ in ] = nextProduced;
      in = ( in + 1 ) % BUFFER_SIZE;
}
```

### Consumer
```c
item nextConsumed;
while( true ) {
     // waits for message
     while( in == out ) {}

     nextConsumed = buffer[ out ];
     out = ( out + 1 ) % BUFFER_SIZE;
}
```
<hr/>

# Message Passing System
Message Passing System allow processors to communicate without using shared memory.
This is useful in distributed system where multiple nodes are connected via networks.

<hr/>

# ETC
Java does not support shared memory

<hr/>

# References
- [https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/3_Processes.html](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/3_Processes.html)
