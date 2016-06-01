title: Memory Management
categories: 
    - Studying Notes
    - Operating System
date: 2016-05-28
---
<img src="https://c7.staticflickr.com/8/7241/27283326326_9cf3700f91.jpg" width="500" height="341">
<!--more-->
----

store data:
* primary storage
* secondary storage

memory management concerns:
* mapping virtual address to real ones
* determining which address is valid
* keep track of which real object are mapped into virtual address
* deciding what to keep in primary storage and what to fetch from else where

aim of address space:
* protect processes from one another
* protect OS from user process
* provide large memory illusion,sharing,new abstraction(pipes, memory-mapped files)

In address space use virtual address, hardware use physical address, OS manage physical address

memory management unit(MMU):
* one level of indirection
* address out of CPU inner core is virtual

<img src="https://c4.staticflickr.com/8/7711/26710117883_0985bdf784.jpg" width="456" height="292">

* MMU translate virtual address into physical address

<img src="https://c6.staticflickr.com/8/7328/26710117973_5febebb30d.jpg" width="500" height="236">

memory fence(single user process):
* if user program access OS area, hardware generate a trap

<img src="https://c2.staticflickr.com/8/7389/26710118073_8af9a7ca3b.jpg" width="500" height="226">

* programmer divide user area to overlay and resident, keep track of which overlay in physical memory

<img src="https://c2.staticflickr.com/8/7094/26710118153_233718be6c.jpg" width="500" height="198">

OS maintain 2 registers for each process:
* base register: start of physical memory
* bounds register: address space size
* address relative to base register
* memory reference independent of base, position independence

<img src="https://c4.staticflickr.com/8/7300/26710118323_469d7d2c5f.jpg" width="500" height="208">

segmentation:
* have base and bounds registers per segment in MMU

<img src="https://c2.staticflickr.com/8/7311/26710118433_9da3cf37e0.jpg" width="500" height="285">

* have access control bits per segment in MMU
* segments could be shared in physical memory
* segmentation fault: virtual address not in range of any base-bounds register

<img src="https://c6.staticflickr.com/8/7328/26710118493_df4e229a3c.jpg" width="500" height="291">

* memory mapped file: map a file(or part of it) into a segment

<img src="https://c8.staticflickr.com/8/7409/26710118623_c1be21551b.jpg" width="500" height="300">

* shared mapping: all processes share same pages in memory, should share text region
* private mapping: initially share data region， when modified by a process, it gets a new and private copy

<img src="https://c2.staticflickr.com/8/7239/26710118753_b1d8df44ba.jpg" width="500" height="291">

* copy on write(COW): process gets private copy of page after performing a write for the first time
* use a validity bit per segment indicating its existance in physical memory(segment can be swapped out, can start with all segments as swapped out)

<img src="https://c4.staticflickr.com/8/7408/26710118843_c5b17d4d37.jpg" width="500" height="285">

address space representation:
* contains many as_region(text, data, bss+heap, mapped file, stack)
* as_region contains: start address, length, access permissioins, shared/private, file object
* every segment need a disk image to swapped out, some kernel memory can be locked down to prevent swapped out

<img src="https://c7.staticflickr.com/8/7113/27283326406_18334b2c48.jpg" width="500" height="166">

## Hardware Support for Virtual Memory

### Forware-Mapped Page Tables

structuring virtual memory:
* segmentation: divide address space into variable-size segments, have external fragmentation, first-fit is slow
* paging: divide address space into fixed-size pages, have internal fragmentation

paging: page table(memory map) to map virtual to physical pages

page frame: maintain information about physical pages, one-to-one mapping
* forward lookup: given virtual address, find page frame
* reverse lookup: given page frame, find process and virtual address

<img src="https://c4.staticflickr.com/8/7323/26710118883_8e3b449b3c.jpg" width="380" height="418">

two-level page table:
* entry contains validity, modified, reference, access protection bits and physical page
* first 20 bits of virtual address used as page number, last 12 bits used as offset in physical page
* analogous to base and bounds registers
* part of process context, sit in physical memory
* physical address of page table is in CR3 register

<img src="https://c8.staticflickr.com/8/7066/26710119023_1750406930.jpg" width="500" height="184">
<img src="https://c4.staticflickr.com/8/7448/26710119123_ee6c08cb51.jpg" width="500" height="231">

page-table size too big:
* hash page tables or forward-mapped page tables
* virtual linear page tables

paged segmentation: 
* 4-bit seg#, decide which segment table
* 16-bit page#, decide which page table
* 12-bit offset

<img src="https://c2.staticflickr.com/8/7065/26710119193_0f974bdb6b.jpg" width="500" height="278">

forward-mapped(multilevel) page table:
* 10-bit page dir#, decide which page dir table
* 10-bit page#, decide which page table
* 12-bit offset
* two physical memory access to map virtual address to physical address

<img src="https://c8.staticflickr.com/8/7239/26710119303_c56084b94d.jpg" width="500" height="279">

### Linear Page Tables

linear page table:
* 2-bit space, 00 and 01 means the entry points to virtual address space, should map again with prefix 10. 10 means the entry points to physical address space 
* 21-bit virtual page number
* 9-bit offset

<img src="https://c4.staticflickr.com/8/7314/26710119363_e1e2414f7b.jpg" width="500" height="207">

* reduce size requirements with partial page table, use length register

<img src="https://c4.staticflickr.com/8/7336/26710119483_8b4fa6f21d.jpg" width="430" height="395">

* not suitable for multithread programming

### Hashes Page Table

hashed page table:
* process page# through hash function, points to a linked list, find right PT entry by comparing tag with page# through list
* only allocated pages are present, works well for sparcely allocated address space

<img src="https://c6.staticflickr.com/8/7400/26710119573_00a08a8949.jpg" width="500" height="277">

clustered page table: similar to hashed page table, each listnode contain 8 PT entry(reducing overhead)

<img src="https://c6.staticflickr.com/8/7568/26710119653_1a5a0afa8f.jpg" width="500" height="290">

inverted page table: 
* all process share a big page table, indexed by physical page number
* each entry contains: PID, page number, physical page number
* map page number to physical page number through hash function

<img src="https://c6.staticflickr.com/8/7303/26710119733_3671c6408f.jpg" width="500" height="260">

### Translation Lookaside Buffer

translation lookaside buffer(TLB): 
* cache page table entries

<img src="https://c8.staticflickr.com/8/7746/26710119783_bc18d18761.jpg" width="500" height="320">

* when switching to different process, must flush entrie TLB

<img src="https://c2.staticflickr.com/8/7134/26710117793_bdc466d35a.jpg" width="500" height="318">

* when an entry is modified, OS must flush corresponding TLB entry

<img src="https://c4.staticflickr.com/8/7017/26710119843_a834e1a1ea.jpg" width="500" height="314">

* 14-bit tag, 6-bit key(how many lines the TLB has), 12-bit offset
* amount of set-associativity is array size in each line, two-way set-associative store two entry in each line

<img src="https://c4.staticflickr.com/8/7595/26710117723_9242b155fd.jpg" width="500" height="129">

* tag in virtual address is compared against all tags in line simultaneously

TLB vs page fault:
* penalty for TLB miss is O(1) memory access
* penalty for page fault is trap into kernel

TLBs and multiprocessors:
* one processor can modify a mapping cached in TLB of another processor
* before modifying mapping, processor 1 must shoot-down TLB of processor 2

<img src="https://c6.staticflickr.com/8/7297/26710117653_d302d9c767.jpg" width="500" height="298">

```C
for all processors i sharing address space:interrupt(i);
for all processors i sharing address space:while(noted[i]==0);
modify_page_table();
update_or_flush_tlb();
down[me]=1;
//shootee interrupt handler
receive_interrupt_from_processor j
noted[i]=1
while(done[j]==0);
flush_tlb();
```

### 64-Bit Issues

x86-64(AMD) virtual address contains 4 page tables: page map table, page directory pointer table, page directory table, page table

<img src="https://c8.staticflickr.com/8/7279/26710117503_354e0d894c.jpg" width="500" height="307">

## Operating System Issues

### General Concerns

fetch policy: bring in pages on demand(lazy evaluation), defer processing until absolutely have to do it

<img src="https://c7.staticflickr.com/8/7317/27283326886_b4c314ac0f.jpg" width="500" height="265">

placement policy: put incoming page in first available physical page

page fault: page table doesn't have requested address

solution for page fault:
* trap occurs
* find free physical page(OS ask buddy system to allocate pages of physical memory(4KB))
* write page out if no free physical page
* fetch page
* return from trap

solution to reduce page fault:
* prefetching: access to pages is often sequential

<img src="https://c7.staticflickr.com/8/7454/27283326846_14915ca19f.jpg" width="500" height="218">

* pageout daemon: a thread to continuously look for free pages, can use multiple

<img src="https://c5.staticflickr.com/8/7193/27283326836_11eb311956.jpg" width="500" height="313">

pageout daemon wants to free up a modified/dirty page:
* find which process the page belongs to
* look at memory map and find corresponding backing store, write back page content to disk
* unmap page from corresponding page table
* free page frame

replacement policy: 
* FIFO: first in first out
* LRU: least recently used
* LFU: least frequently used

LRU: reference bit in page table entry is used
* two-handed clock algorithm: two hands scan entry, front hand make reference bit = 0, after enough time, back hand check reference bit, remove page whose bit = 0

<img src="https://c3.staticflickr.com/8/7511/27283326746_03a39dfb3a.jpg" width="500" height="291">

manage primary storage:
* global allocation: all processes compete for page frames from a single pool
* local allocation: give each participant a minimum amount of storage, leave additional storage available for all to compete

working-set principle: 
* WS(P,T) is the set of pages used by process P over time T
* if the amount of space isn't available, P shouldn't run and should be swapped out

### Representative Systems

memory allocation:
* user: virtual allocation
* OS kernel: virtual and real allocation
* when allocating page frame for user process, these pages are mapped both into user address space and kernel address space
* OS code and data stay still in address space when switching process
* 1GB real memory: OS mapped identically and kernel don't need its own page table

<img src="https://c5.staticflickr.com/8/7329/27283326716_ba4809fba7.jpg" width="500" height="179">

* lots of real memory: 896MB OS mapped identically, 1/8 kernel address space use kmap to map other part in real memory

<img src="https://c3.staticflickr.com/8/7346/27283326666_90e795e740.jpg" width="500" height="291">

linux divide physical memory into 3 zones:
* DMA zone: locations < 2^24, DMA devide handle 24-bit address
* Normal zone: locations < 2^30 - 2^27, OS data reside here
* HighMem zone: locations > 2^30 - 2^27, strictly for user pages

each zone's page divided into 3 list:
* free list: buddy system to maintain
* inactive: picked out by clock algorithm as not recently used, dirty/modified
* active: picked out by clock algorithm as recently used

<img src="https://c1.staticflickr.com/8/7323/27283326576_d537e5cc1f.jpg" width="500" height="360">

### Copy on Write and Fork

fork()/exec() problem:
* naive implementation: actually make copy of parent's address space, waste much time
* vfork(): give address space to child, efficient, but only works if child does an exec

<img src="https://c5.staticflickr.com/8/7370/27283716276_bfae86e4d1.jpg" width="500" height="310">

* copy on write(lazy evaluation): after fork, r/w change to ro of access control for private page. equivalent to original fork(), faster than it

shadow object: 
* one level of indirection between segment and file object

<img src="https://c7.staticflickr.com/8/7008/27283326526_2794cdf909.jpg" width="257" height="465">

* keep track of pages that were originally copy-on-write but have been modified
* if page referenced in shadow object, it has been modified
* tell where to copy from when need to perform copy-on-write
* for original object, not all page have to be resident, for shadow object, all page have to be resident
* a variable can exist in many different physical pages simultaneously, each contain a different version

<img src="https://c1.staticflickr.com/8/7358/27283326496_41a89bd5ff.jpg" width="500" height="297">

### Backing Store Issue

type of mmobj(used to manage page frame):
* file object(vnode)
* shadow object
* anonymous object

dynamic/heap and stack region initialized with zeros, known as anonymous pages

backing up pages:
* read-only mapping and read-write shared mapping of files are written back to file
* file referenced in shadow objects and anonymous objects are backed up in swap space

swap space:(windows: reserved+commited)
* radical-conservative approach: eager evaluation, backing-store space is allocated when virtual memory is allocated
* radical-liberal approach: lazy evaluation, backing-store space is allocated only when needed