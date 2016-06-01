title: Operating-System Design
categories: 
    - Studying Notes
    - Operating System
date: 2016-05-24
---
<img src="https://c2.staticflickr.com/8/7785/27236221225_908bbf2490.jpg" width="500" height="253">
<!--more-->
----

OS components:
* processor management
* memory management
* I/O management

	<img src="https://c2.staticflickr.com/8/7779/26630610713_6e2b4d1e67.jpg" width="500" height="351">

purpose of process:
* hold address space
* hold a group of threads
* hold references to open files nad other execution context

address space: set of addresses that threads can usefully reference

categories of I/O:
* block I/O: mapping files into address space to perform I/O, block/page is basic unit
* sequential I/O: use system call(read() and write()) to perform I/O
* read a file like reading from the keyboard, produce output should also use same code to write to a file or wirte to a network

Hardware Abstraction Layer(HAL): 
* a portable interface to machine configuration and processor-specific operations within the kernel
* most OS is machine and processor independent

	<img src="https://c4.staticflickr.com/8/7444/26630610803_7f73a7bfbd.jpg" width="500" height="314">

porting OS to new computer:
* writing new HAL routines
* relink with kernel

## Monolithic Kernel

### A Framework for Devices

device is identified by two numbers:
* major device number: identifies the device driver
* minor device number: device index for all devices managed by this device driver

	<img src="https://c6.staticflickr.com/8/7589/26630610893_ff840ae996.jpg" width="500" height="353">

devices are refered in /dev direcory in file system, as special file, don't contain data

approach to set up device: 
* statically allocated array(sdevsw): 
 * kernel was statically configured to contain device-specific information
 * kernel must custom configured for each installation
* device probing: 
 * allow device to be found and automatically configured when booting
 * kernel still contain all device drivers
 * each device driver includes a probe routine
 * allow kernel image built for a number of similar installations not identical ones
 * boot time is long, impractical as supported devices too much
* meta-drivers: 
 * meta-driver handle a particular kind of bus
 * installed into kernel
 * device interact with meta-driver via USB protocol
 * connect and select appropriate device driver loading into kernel

### Processes & Threads

process life cycle: run(start)->zombie

process relationships:
* have a process hierarchy, root is init process
* when one process exit, its children link to init process

	<img src="https://c2.staticflickr.com/8/7454/26630610953_c447e76bc1.jpg" width="500" height="314">

when forking, only copy current thread, unlock all mutex before fork()

<img src="https://c2.staticflickr.com/8/7740/27236222105_26f024b631.jpg" width="500" height="88">

thread life cycle:
* runnable(start)->running: scheduler switch
* running->runnable: scheduler switch
* running->waiting: blocking call
* waiting->runnable: unblocked by another thread or interrupt handler
* running->terminated: pthread_exit()

	<img src="https://c5.staticflickr.com/8/7334/26961087060_24f4f731f3.jpg" width="500" height="215">

deleting TCB and stack space:
* if not detached: the thread call pthread_join()
* if detached: 
 * use a special reaper thread
 * queue threads on list and other threads free them if convenient

### Low-Level Kernel

terminal: client program interact with a remote system, login session is on the target machine

interact with terminal device:
* characters displayed sent to output routine of serial-line driver
* make a call to input routine to fetch characters typed at keyboard
* not straight-forward
* need output buffer and input buffer for characters

application thread place characters on output queue and take from input queue

<img src="https://c3.staticflickr.com/8/7499/26961087210_4335716cfb.jpg" width="500" height="290">

use interrupt handler for keyboard activity:
* in read-completion interrupt, handler move character from device to input queue, issue another request to device and block, must mask interrupt when taking characters
* in write-completion interrupt, handler move character from output queue to keyboard, issue another request to device. if application write to empty queue, setup handler

two input queues:
* one for partial-line, subject to editing
* the other contain characters from complete lines
* handler move character from device to partial-line queue
* if input is carriage-return, entire content of partial-line queue move to completed-line queue

for reusability, a separate module, line-discipline, provide common character-handling code, interact with any device driver

<img src="https://c1.staticflickr.com/8/7451/26961087280_322c2fcecb.jpg" width="500" height="268">

put terminal driver and line-discipline module in kernel

modern system about terminal:
* bit-mapped displays, keyboards, mice connected via USB
* window manager implements display, determine applications receiving input
* applications can receive input, send output over network
* use pseudoterminals

	<img src="https://c5.staticflickr.com/8/7612/26961087420_8cff958ca2.jpg" width="500" height="258">

network communication:
* device is Network Interface Card(NIC)
* data in packet
* data processed via network-protocol modules, protocols are layered on top of one another

	<img src="https://c7.staticflickr.com/8/7312/26961087470_8805a32cd8.jpg" width="500" height="319">

performance about network data:
* pass packet from one module to next without copying data
* append/remove headers
* hold on packets for possible retransmission
* request and respond to time-out

solution:
* create pointer point to head and data of packet in each layer
* copy and move pointer, not packet
* use callback mechanism to implement timeout

	<img src="https://c6.staticflickr.com/8/7284/26630611453_e4d4b19d45.jpg" width="500" height="340">

## Rethinking Operating-System Structure

monolithic kernel:
* good performance, but bad reliability
* solve it by virtual machines/microkernel

### Virtual Machine

virtual machine: applications, OS, virtual machine, virtual machine monitor(VMM), hardware

<img src="https://c6.staticflickr.com/8/7419/26630611573_dedb030cb9.jpg" width="500" height="239">

VMM is a synchronization construct allow executing entities to have mutual exclusioin and ability to wait(block) for a condition becoming true

virtual machine is abstraction of hardware

entire virtual machine in user mode of the real machine, VMM in privileged mode of the real machine

VMM keep track of whether virtual machine in virtual privileged mode(OS) or virtual user mode(application)

process in virtual machine is real processor:
* instuctions are executed
* traps are generated just as on real machine:
 * trap handler indexed by trap number in hardware-mandated jump table in virtual machine
 * VMM find address of virtual machine's trap handler and transfer control to it

virtual machine(not equal to software emulator) contain every hardware components represented by data structures and code

sensitive instruction: an instruction will change something in the hardware/processor, must be privileged instruction

advantage of virtual machine:
* good structuring technique for multi-user system
* good for debugging and testing
* adapt to hardware change in software
* multiple OSes on one machine
* server consolidation and service isolation

actions on real machine:

| (none)                    | user mode                                        | privileged mode |
|:-------------------------:|:------------------------------------------------:|:---------------:|
| non-sensitive instruction | execute fine(communicate with hardware directly) | executes fine   |
| errant instruction        | traps to kernel                                  | traps to kernel |
| sensitive instruction     | traps to kernel                                  | executes fine   |

actions on virtual machine:

| (none)                    | virtual user mode                                  | virtual privileged mode                      |
|:-------------------------:|:--------------------------------------------------:|:------------------------------------------------------:|
| non-sensitive instruction | executes fine                                      | executes fine                      |
| errant instruction        | traps to VMM; VMM causes trap to occur on Guest OS | traps to VMM; VMM causes trap to occur on Guest OS |
| sensitive instruction     | traps to VMM; VMM causes trap to occur on Guest OS | traps to VMM;VMM verifies and emulates instruction  |

virtualization:
* full virtualization
* paravirtualization
* containers

difference on IBM and Intel:
* IBM have all sensitive instructions are privileged instructions, Intel don't have all
* IBM's I/O operation done via channel programs in memory, Intel's done via memory-mapped addresses

rings: Intel have 4 modes: kernel, 1, 2, apps

<img src="https://c6.staticflickr.com/8/7659/26630610573_aaa13a85d4.jpg" width="460" height="443">

solution for Intel sensitive instructions:
* binary rewriting(vmware): replace sensitive instructions with hypercall(trap to VMM)
* hardware virtualization: create root mode
* paravirtualization(Xen): modify guestOS code, hypervisor call

solution for Intel I/O operation:
* vmware:
 * host/guest model: VMM part of hostOS,VMApp
 * split driver in VMM

	<img src="https://c5.staticflickr.com/8/7290/26629455204_65d8507e18.jpg" width="500" height="320">

* Xen:
 * no driver in guest

	<img src="https://c8.staticflickr.com/8/7562/26630610463_12338fdec0.jpg" width="500" height="320">

VM memory:
* process: virtual virtual memory
* Guest OS: virtual real memory
* VMM: real memory

solution for mapping:
* each VM has page table, VMM has own page table, merge into shadow page table(for each VM)
* when updating VM's page table, shadow page table must update
* poor performance

	<img src="https://c8.staticflickr.com/8/7624/26630610303_fe0ae04b77.jpg" width="500" height="284">

solution:
* direct translation: make a hypercall when updating
* extended page table: traverse two tables in sequence and converse it by itself(visit memory 4 times)

### Micro Kernel

microkernels contains:
* process management
* memory management
* device drivers
* message passing

messaging service: client create request port, response port and capability(key) to send data

<img src="https://c4.staticflickr.com/8/7305/27236221355_28cc1bb87a.jpg" width="500" height="331">