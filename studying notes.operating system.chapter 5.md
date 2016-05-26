title: Processor Management
categories: 
    - Studying notes
    - Operating System
date: 2016-05-25
---
<img src="https://c5.staticflickr.com/8/7418/26647571444_3d067112a5.jpg" width="500" height="430">
<!--more-->
----

## Threads Implementations

### Strategies

threads implementation:
* one-level model(kernel): variable-weight processes

	<img src="https://c4.staticflickr.com/8/7624/27184610091_f899156e21.jpg" width="500" height="322">

* two-level model(user library):
 * Nx1: single kernel thread(per user process)
 * MxN: multiple kernel threads(per user process)
 * scheduler activations model

one-level model: 
* each user thread mapped one-to-one to kernel thread
* kernel create TCB, PCB, kernel/user stack
* all operation in kernel, system calls are expensive
* variable-weight process(variant): portions of parent process selectively copied into or shared with child process

	<img src="https://c4.staticflickr.com/8/7346/27184610211_eaba755622.jpg" width="500" height="283">

Native POSIX-threads library:
* threads of a process form thread group
* any thread in group can wait for other to terminate
* signal delivered to any thread in group
* futex(fast user space mutex): if mutex available, return quickly with mutex locked, if not, make a system call and block in kernel

N to 1 model:
* threads implemented entirely in user level
* kernel don't know the existance of user threads
* fast, no system call for thread-related APIs
* but if having system call, no other user thread can run

	<img src="https://c2.staticflickr.com/8/7691/27184610321_aaeec10a94.jpg" width="500" height="334">

solution: have a non-blocking read() called real_read()

```C
ssize_t read(int fd, void *buf, size_t count){
	ssize_t ret;
	while(1){
		if((ret=real_read(fd,buf,count))==-1){
			if(errno==EWOULDBLOCK){
				sem_wait(&FileSemaphore[fd]);
				continue;
			}
		}
		break;
	}
	return(ret);
}
```

M to N model: if don't have enough kernel threads per process, end up having same problem with N-to-1 model

<img src="https://c2.staticflickr.com/8/7273/27184610041_e32a077d33.jpg" width="500" height="286">

problem: deadlock(automatically create new kernel thread), priority inversion

scheduler activations model: process determine which threads get to use, processor determine which process get to use

<img src="https://c5.staticflickr.com/8/7440/27220521676_42a3117c81.jpg" width="500" height="327">

### A Simple Thread Implementation

concurrency control:
* interrupt handler running on same processor accessing same data
* another thread running on same processor may preempt this thread, accessing same data
* interrupt handler running on another processor accessing same data
* another thread running on another processor accessing the same data

	<img src="https://c2.staticflickr.com/8/7720/27184609961_f83607aecf.jpg" width="500" height="208">

each thread must be in CPU or a queue
<img src="https://c2.staticflickr.com/8/7137/27184609921_e32a077d33.jpg" width="500" height="289">

if thread not currently running, stack frame corresponds to switch()
<img src="https://c2.staticflickr.com/8/7781/27184609801_8d7410c3d2.jpg" width="401" height="361">

concurrency control (2) solution:

```C
void thread_switch(){
	thread_t nextthread,oldcurrent;
	nextthread=dequeue(runqueue);
	oldcurrent=currentthread;
	currentthread=nextthread;
	swapcontext(&oldcurrent->context,&nextthread->context);
}
void mutex_lock(mutex_t *m){
	if(m->locked){
		enqueue(m->queue,currentthread);
		thread_switch();
	}else
		m->locked=1;
}
void mutex_unlock(mutex_t *m){
	if(queue_empty(m->queue))
		m->locked=0;
	else
		enqueue(runqueue,dequeue(m->queue));
}
```

### Multiple Processors

avoid boundary condition: run on each processor an idle thread

```C
void idle_thread(){
	while(1){
		enqueue(runqueue,CurrentThread)
		thread_switch()
	}
}
```

concurrency control (4) solution:
* spin lock: waiting time small
* blocking lock: waiting time long
* futex: optimized blocking lock

hardware support, compare and swap machine instruction:

```C
int CAS(int *ptr, int old, int new){
	int tmp=*ptr;
	if(tmp==old)
		*ptr=new;
	return tmp;
}
```

CAS implemented as a machine-level instruction, execute atomically
<img src="https://c4.staticflickr.com/8/7673/27184609691_d394987e61.jpg" width="500" height="256" alt="unlocked">
<img src="https://c8.staticflickr.com/8/7399/27184609751_5c76c3ebfe.jpg" width="347" height="278" alt="locked">

spin lock:

```C
void spin_lock(int *mutex){
	while(CAS(mutex,0,1));
}
void spin_lock(int *mutex){
	while(1){
		if(*mutex==0){
			if(!CAS(mutex,0,1))break;
		}
	}
}
void spin_unlock(int *mutex){
	*mutex=0;
}
```

futex:
* two system calls are provided to support futex:

```C
futex_wait(futex_t *futex,int val){
	if(futex->val==val)sleep();
}
futex_wake(futex_t *futex){}
```

* ancillary functions:

```C
unsigned int atomic_inc(unsigned int *val){
	return ((*val)++);
}
unsigned int atomic_dec(unsigned int *val){
	return ((*val)--);
}
```

* complete futex code:

```C
void lock(futex_t *futex){
	unsigned int c;
	if((c=CAS(&futex->val,0,1))!=0)
		do{
			if(c==2||(CAS(&futex->val,1,2)==1))
				futex_wait(futex,2);
		} while((c=CAS(&futex->val,0,2))!=0);
}
void unlock(futex_t *futex){
	if(atomic_dec(&futex->val)!=1){
		futex->val=0;
		futex_wake(futex);
	}
}
```

## Interrupts
<img src="https://c1.staticflickr.com/8/7455/26978907840_dd26b3cc1f.jpg" width="500" height="322">

non-preemption kernel: a kernel thread never be perrmpted by another thread

dealing with perrmption kernel:
* disable preemption
* use interrupt masking
* spin lock

interrupt masking:
* unmasked interrupts can interrupt current thread
* cause: occurrence of particular class of interrupt; explicit programmatic action
* hierarchy of interrupt levels

concurrency control (1) solution:

```C
int disk_write(){
	oldipl=setipl(diskipl);
	startIO();
	enqueue(disk_waitq,currentthread);
	thread_switch();
	setipl(oldipl);
}
void thread_switch(){
	thread_t *oldthread;
	int oldipl;
	oldipl=setipl(high_ipl);
	while(queue_empty(runqueue)){
		setipl(0);
		setipl(high_ipl);
	}
	oldthread=currentthread;
	currentthread=dequeue(runqueue);
	swapcontext(oldthread->context,currentthread->context);
	setipl(oldipl);
}
```

concurrency control (3) solution:

```C
void accessxthread(){
	disablepreemption();
	maskinterrupts();
	spinlock(&L);
	x=x+1;
	spinunlock(&L);
	unmaskinterrupts();
	enablepreemption();
}
void accessxinterrupt(){
	spinlock(&L);
	x=x+1;
	spinunlock(&L);
}
```

deferred work:
* interrupt handler only do what must do
* defer most of work to be done after interrupt handler return

deferred processing:

```C
void toplevelinterrupthandler(int dev){
	interruptvector[dev]();
	if(previouscontext==threadcontext){
		unmaskinterrupts();
		while(!empty(workqueue)){
			work=dequeue(workqueue);
			work();
		}
	}
}
void networkinterrupthandler(){
	enqueue(workqueue,morework);
}
```

windows handle deferred work in a special interrupt context, DPC(deferred procedure call), a software interrupt
<img src="https://c4.staticflickr.com/8/7451/27184609531_5e8ceac9ae.jpg" width="449" height="483">

```C
void InterruptHandler(){
	QueueDPC(MoreWork);
	/*request a DPC interrupt*/
}
void DPCHandler(){
	while(!Empty(DPCQueue)){
	Work=DeQueue(DPCQueue);
	Work();
	}
}
```

linux handle deferred work in a special kernel thread

```C
void interrupthandler(){
	queue(workqueue,morework);
	setevent(work);
}
void softwareinterruptthread(){
	while(true){
		waitevent(work);
		while(!empty(workqueue)){
			work=dequeue(workqueue);
			work();
		}
	}
}
```

signal: perform given action in context of a particular thread in user mode
APC: windows asynchronous procedure call, roughly same, but may be done in kernel mode

signal handler: set up user stack(save&restore registers, signal mask) and handler as a subroutine
* save user thread context in a sigframe on user stack

	<img src="https://c7.staticflickr.com/8/7147/26647571814_67e3246188.jpg" width="500" height="344">

## Scheduling

sort of system:
* simple batch
* multiprogrammed batch
* time sharing
* partitioned server
* real time: hard real time/soft real time
* general purpose

scheduling goal:
* maximize CPU utilization
* maximize throughtput: CPU-bound, I/O bound
* minimize wait time
* minimize response time
* fairness

scheduling algorithm:
* basic: FIFO, SJF, SRTN, RR
* priority: multi-level, multi-level with feedback
* portional share: lottery, stride
* real time: EDF, rate monotonic

FIFO: no preemption, running time is known

SJF: shortest job first, non-preemption, starvation(short job keep arriving)

SRTN: shortest remaining time next, preemption

RR(round robin/time-slicing): q->0, not realistic, translation lookaside buffer flushing and caching problem
<img src="https://c1.staticflickr.com/8/7396/26647571664_6c5a83b3a3.jpg" width="500" height="361">

max-min fairness: a fair service maximize the service of the customer receiving the poorest service(goal of RR)

priority queueing: for interactive job, multi level queues, each queue use RR

multi-level feedback queue: time-sliced, priority-based, preemptive
* when thread created, get highest priority
* if using a full time slice, decrease priority
* if block before using up full time slice, increase priority
* aging: if a job hasn't run for long, increase priority

	<img src="https://c4.staticflickr.com/8/7341/27184609331_7e4f90e93c.jpg" width="500" height="436">

lottery scheduling: a lottery is held for every scheduling decision

stride scheduling: time-sliced, priority-based, preemptive
* every thread assigned a priority named pass value, single queue, smallest pass value thread first
* every thread assigned a stride value, computed according to distribution of tickets in a lottery scheduling scheme
* shedule thread with smallest pass value
* set global pass value to be the pass value of new coming thread
* increment thread's pass value by stride value when finishing running
* loop
* interactive threads get higher priority

in hard real time system:
* find schedule satisfying constraints
* uniprocessor:
 * rate-monotonic scheduling of cyclic chores
 * earliest deadline first(EDF)
* interrupt don't interfere with schedule, execution time is predictable

rate-monotonic: 
* each chore is handled by a thread with priority 1/P
* preemptive, priority scheduling

	<img src="https://c1.staticflickr.com/8/7304/26647571504_2ebf8ede53.jpg" width="500" height="260">

priority inversion: high-priority thread A block on mutex 1, low-priority thread C hold mutex 1, C cannot run because medium-priority thread B is running

solution: give thread C the same priority as thread A

multiple processors:
* cache affinity: a thread running on same processor every time would be cheaper
* use one run queue per processor
* scheduler may do load balancing