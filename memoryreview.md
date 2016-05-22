title: Memory Management Review
tags: [OS, virtual memory]
categories: 
	-Operating Systems
date: 2016-05-21
description: This is a review of memory management, and focus on virtual address concept, different ways of memory management, TLB, shadow object, backing store issues.
---


## Virtual Memory Basic

### Virtual Address Concept
- Protect processes from on another
- Protect the OS from user processes
- Provide efficient management of available storage
-  Use a virtual address to address any memory location in the 32-bit address space
- Only headware use physical address(include processor) and OS manages the physical address space
- Memory Management Unit (MMU), is used for indirect address translation. It translated virtual address into physical address.
	<img src="https://farm8.staticflickr.com/7534/26980709472_21687456f0_b.jpg" width="400" height="200" alt="MMU">

### Memory Management in the Early Days

- Memory Fence
	- If a user program tries to access OS area (address bigger than fence address) MMU will generate a trap	
	- use overlays and resident to deal with user program won't fit in memory

- Base and Bounds Registers
	- Bounds register: Address space size of the user process
	- Base register: Start of physical memory for the user process
	- Segmentation:
		- One pair of base and bounds registers per segment.
		- code, data, heap, stack and may be more (Memory Mapped file).
		- Use one bit to have access control (read-only, read/write).
		- If two prcesses read same memory, they can share segments.
		- Segmentation fault: virtual address not within range of any base-bounds registers or not valid.
		- Copy-On-Write: a process gets private copy of the page after a thread in the process performs a write for the first time (private/share bit need not be inside MMU).
		- Use a validity bit for each segment to find whether this segment is in memory or being swapped out before.

## Hardware Support for Virtual Memory
	
- Segmentation divide the address space into variable-size segments and it has external fragmentation problem and "first-fit" is slow.
- Paging divede the address space into fixed-size pages and can have internal fragmentation problem.
- Paging:
	- Address space is divided into pages, indexed by virtual page number.
	- Basic (Two-level) Page table indexed by virtual page number, start with a physical address (this is stored in the CR3 register for X86), has validity, modified, reference and protect bit, and physical page number.
	- A page table is associated with each process (OS has its page table as well).

### Forward-Mapped (Multi-level) Page Tables

<img src="https://farm8.staticflickr.com/7400/27076290915_04e7cb14b9_b.jpg" width="400" height="220" alt="FM page table">

- Use 10 bits for page dir table and 10 bits for page table and the last 12 bits use for offset.
- Can save page table size, the minimum size is 12KB (one page dir two page tables)
- The drawback is it has two physical memory accesses to map a virtual address to a physical address

### Linear Page Tables

- Use 2 bit as Space, 00 and 01 page table are start with virtual address space and conbine the 00 or 01 bits, in 10 space page table started with physical address we can find page table entry.
- Can reduce size requirements with partial page tables, and it can use length register to constranins size of each space. But it not work well for multiple processes.

### Hashed Page Tables

- Hashed page tables
	- Use 2 bits for hashing
	- Works well for sparcely allocated address space
	- Each page table entry have a tag and link
- Clustered page tables
	- Many page table entries have one tag and link
- Inverted page tables
	- Page table is indexed by physical page number
	- Conbine virtual page number and PID to get hash
	- Each entry contains PID, page number and physical page number

### Translation Lookaside Buffers (TLB)

- Cashe page table entries, caches the mapping from virtual page nmber to physical page number.
- TLB miss will cost O(1) memory accesses.
- When a page table entry is modified, the OS must flush (invalidate) the corresponding TLB entry. When switching to a different process, must flush the entire TLB.
- In a multiprocessors environment, one processor can modify a mapping cached in the TLB of another processor by shoot-down (invalidate) the another processor.
- Two-way set-associative cache with 64 lines:	
	- Use 6 bits as key to have 64 lines.
	- In same the line, tag in the virtual address is used to compared against all other tags in same line simutaneously.
	- For other TLB, it can be fully associative cache or one-way set- associative cache.

### 64-Bit Issues

- Have more tables which need more physical memory accesses to map a virtual address to a physical address

### Virtualization (virtualizing virtual memory)

- Can use shadow page table, when a VM changes its page table, VMM must update the corresponding shadow page table. But it has poor per formance.
	
	<img src="https://farm8.staticflickr.com/7427/26982039952_1efe21c539_o.png" width="400" height="220" alt="shadow page table">

- Use Extended Page Tables, two table in sequence and does the conversion all by itself.

	<img src="https://farm8.staticflickr.com/7675/26472922783_48b1352333_b.jpg" width="400" height="250" alt="shadow page table">


## Operating System Issues

### General Concerns

- Prefetching	
	- Fetching a page as well as the sequential pages.
	- Access to pages is often sequential.
- Pageout Daemon
	- Reference bit in page table entry is used for approximate LRU.
	- Clock algorthm, need to give enough time for thousands of references before checking
	<img src="https://farm8.staticflickr.com/7366/26982432522_b89cb1fe31_b.jpg" width="420" height="280" alt="Clock algorthm">
- Thrashing problem
	- Global allocation: All processes compete for page frames from a single pool.
	- Local allocation: Each process has its own private pool of page frames. Using Local Allocation is a way to reduce the chance of thrashing.
	- For thrashing problem, use Working-Set Principle, if the sum of the working-set of all processes is less than the total amount of available physical memory then thrashing cannot occur.

### Representative Systems

- Address-Space Simplified Representation:	

	<img src="https://farm8.staticflickr.com/7728/27008966701_abf3d06926_b.jpg" width="420" height="250" alt="AS simple pres">

- Address-Space Real Representation:

	<img src="https://farm8.staticflickr.com/7195/27008966741_f2cc810af9_b.jpg" width="420" height="270" alt="AS real pres">




### Copy on Write and Fork

- Shadow Objects:
	- Keep track of pages that were originally copy-on-write but have benn modified.
	- A page in a memory map, into which an object was mapped private, have an associated shadow object.
	- Shadow object tells you where to copy from when you need to perform copy-on-write.

		<img src="https://farm8.staticflickr.com/7431/26473555803_b050ed08d4_b.jpg" width="420" height="250" alt="Clock algorthm">

### Backing Store Issues

- Shadow objects and anonymous memory must be backed up in swap space.
- Swap Space management approach:	
	- randical-conservative approach: eager evaluation, backing-store space is allocated when virtual memory is allocated.
	- radical-liberal approach: lazy evaluation, backing-store space is allocated only when needed.


<!-- Go to www.addthis.com/dashboard to customize your tools -->
<div class="addthis_sharing_toolbox"></div>
