title: Multithreaded Programming
categories: 
    - Studying Notes
    - Operating System
date: 2016-05-21
---
<img src="https://farm8.staticflickr.com/7236/27058898412_5ae7d856a7.jpg" width="500" height="306">
<!--more-->
----

why using threads rather than just process:
* multithreading is a powerful paradigm
* make design cleaner and less buggy
* run faster without explicitly giving up CPU

threads could be implemented in user or kernel space, both have some advantages and disadvantages

### Threads Creatioin & Termination

The way to create a POSIX thread: int pthread_create (pthread_t \*thread, const pthread_attr_t \*attr, void \*(\*start_routine) (void \*), void \*arg)
* start_routine is also known as first procedure or thread function, child thread start and end from it
* thread id will be stored in the first argument, may in thread control block

each thread need its own stack in process address space

	<img src="https://farm8.staticflickr.com/7115/27091257741_e5afd15966.jpg" width="263" height="473">

The way to wait for another thread to terminate: int pthread_join (thread_t thread, (void \*\*) ret_value)
* any thread can join with any other thread
* if not pthread_join, the terminated thread goes into a zombie state(free everything except thread control block)

How thread get self-terminate:
* return from first procedure
* call pthread_exit(ret_value)

exit() terminate a process, while pthread_exit() terminate a thread

### Synchronization

use mutex for synchronization:
* code between pthread_mutex_lock() and pthread_mutex_unlock() for a mutex is called critical section with respect to that mutex
* all critical sections with respect to a particular mutex are mutually exclusive
* The way to set up a mutex: pthread_mutex_t m = PTHREAD_MUTEX_INITIALIZER;

necessary conditioins for deadlock(mutex 1 wait for mutex 2 and mutex 2 wait for mutex 1):
* bounded resources
* wait for resources
* no preemption
* circular wait

	<img src="https://farm8.staticflickr.com/7318/27091257681_8a9f29607a.jpg" width="500" height="239">

solutions for deadlock:
* lock hierarchies: organize mutex into levels, can only lock higher level mutex than its holding mutex
* conditional locking: use pthread_mutex_trylock()

when having one mutex but multiple critical sections: threads can be placed into a queue and wait indefinitely for mutex to become available

guarded command: 
* when(guard)[]
* guard+command sequence together is one critical section which can be executed atomically without interruption

	<img src="https://farm8.staticflickr.com/7476/27091257441_1358880c18_z.jpg" width="640" height="177">

semaphores: nonnegative integer
* P(S) operation: when(S>0)[S=S-1;]
* V(S) operation: [S=S+1]
* categoried as binary semaphore/counting semaphore(initial value differs)

difference between semaphore and mutex:
* one thread lock a mutex, this thread should unlock it
* one thread perform P on semaphore, another thread perform V on it
* mutex is coarse grain, semaphore is fine grain

condition variables: a queue of threads waiting for guard to become true
* wait for specific condition to be signaled
* wait for right time to evaluate guard
* the way to set up a condition variable: pthread_cond_t cv = PTHREAD_COND_INITIALIZER;

	<img src="https://farm8.staticflickr.com/7617/27091257571_b9e4314798.jpg" width="306" height="407">

wake up condition variables:
* threads do something potentially change guard
* signal or broadcast the condition
* no guarantee the guard will be true

execute pthread_cond_wait (pthread_cond_t \*cv, pthread_mutex_t \*mutex)
* when guard is not true
* after mutex is locked can you call this routine
* atomically unlock mutex and wait for signal/broadcast
* when signal/broadcast, return with mutex locked to mutex queue

thread should call signal/broadcast (change condition variables) only after mutex is locked

with respect to mutex, a thread could be:
* waiting in the mutex queue
* inside the synchronization box
* waiting in the condition variable queue
* outside

POSIX condition variables implementation:
```C
pthread_mutex_lock(&mutex);
while(!guard)
	pthread_cond_wait(&cv, &mutex);
statement;
pthread_mutex_unlock(&mutex);

pthread_mutex_lock(&mutex);
pthread_cond_broadcast(&cv);
pthread_mutex_unlock(&mutex);
```

conception about barriers: when a thread reaches barrier, it stop and wait for other threads to arrive, then all threads are given signal to proceed and barrier is reset

	<img src="https://farm8.staticflickr.com/7314/27091257291_3b11013a6e_z.jpg" width="640" height="309">

### Thread Safety

thread safety:
* make unithreading libraries safe to run under multithreading
* not equal to reentrant, reentrant code applies to single thread case as well

problem and solution with old unix API:
* global variables:
 * store thread-specific data in TCB
 * add reentrant version of system call, like provide a buffer to hold return data
* shared data:
 * wrap library calls with synchronizatioin constructs
 * fix the libraries
 * application can use a mutex

### Deviations

deviation type:
* signal mechanism: ask another thread to deviate from its normal execution path
* cancellation mechanism: force another thread to terminate

signal:
* not software interrupt, performing upcall by OS
* in response to exceptions, external events and user defined events
* can have effect of termination, invocation of a procedure, suspension or resumption

	<img src="https://farm8.staticflickr.com/7160/27091257081_07ea5fa033_z.jpg" width="640" height="558">

a signal is pending if generated but blocked, when unblocked, it's delievered

send signal to process:
* int kill (pid_t pid, int sig)
* type Cntrl+C
* kill shell command
* do something illegal
* int pthread_kill (pthread_t thr, int sig): send signal to thread

signal handler:
* each signal in a process have one handler
* specify a signal handler: sigset(), signal(), sigaction()
* could re-establish signal handler inside signal handler so you can receive the signal more than once

async-signal safety:
* make code safe working with asynchronous signals
* let any data structure async-signal safe: async signal cannot corrupt data structures
* make async-signal synchronous: use another thread to receive particular signal

signal mask:
* stored in TCB
* a set of signals represented as a set of bits, 1 means blocked
* change signal mask: int sigprocmask (int SIG_BLOCK/SIG_UNBLOCK/SIG_SETMASK, const sigset_t \*set, sigset_t \*old);
* sigset_t: sigemptyset(), sigaddset(), sigdelset()

sigsuspend()/sigwait(): atomically unblocks signal and waits for it

In multi-threaded process, signal is delivered to a thread chosen at random, could block other unrelative threads to solve it

signal generated while a process is blocked in a system call: interrupt system call, deal with signal, return from system call with indication that something happened

in general, should only do what's absolutely necessary inside a signal handler

cancellation:
* user pressed Cntrl+C or a request is generated
* not execute the remaining thread, but not the process
* concern cancell it to a consistent state, cleaning up
* pthread_cancel(thread)
* int pthread_setcancelstate ({PTHREAD_CANCEL_DISABLE, PTHREAD_CANCEL_ENABLE}, &oldstate)
* int pthread_setcanceltype ({PTHREAD_CANCEL_ASYNCHRONOUS, PTHREAD_CANCEL_DEFERRED}, &oldtype)

cancellation rules:
* after calling pthread_cancel(), caller don't wait for the cancellation, callee marked as a pending cancel
* if callee is cancellation disabled, it stay in pending cancel state
* if callee is enabled and asynchronous, it immediately acts on the cancel
* if callee is enabled but deferred, it delayed until reaches a cancellation point where safe to act on cancel
* pthread_testcancel() can create a cancellation point
* when act on cancel, walk through a stack of cleanup handlers: pthread_cleanup_push(), pthread_cleanup_pop(), the two procedure must match up