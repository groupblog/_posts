title: File Systems
tags: [OS, file systems]
categories:
	- Studying Notes
	- Operating Systems
date: 2016-05-21
---

### Introduction

- This is a review of file systems, and focus on S5FS, multiple disks, flash memory, crash Resiliency, naming and dirctories.
- The requirements of file systems:
	- Permanent storage
	- Quick, easy and efficient

<!--more-->
----

### The Basics of File Systems

#### Disk Architecture

<img src="https://farm8.staticflickr.com/7587/26889680562_7ae35ccfab_h.jpg" width="400" height="280" alt="disk_architecture">


- Have multiple platters and each one have top and bottom heads.
- The smallest addressable unit is a sector. Each platter contains many tracks and each track contains many sectors. The same vertical's tracks is one cylinder.
- disk address = (head/surface#, cylinder/track#, sector#)

#### S5FS

Simple, slow, don't terribly tolerant to crashes, reasonably efficient in space although no compression

1. layout

	<img src="https://farm8.staticflickr.com/7671/26983710105_ec8f5d2828_b.jpg" width="170" height="250" alt="S5FS">

	- The layout of the disk simply an array of blocks of 1KB each and it starts with Boot block, Superblock and then it contains i-list, which has many blocks and keep all the inode information. The final part is Data Region, which keep all the real data.

2. Superblock
	- Contains the head of the free list:
		Can address up to 100 free disk blocks, and the last pointer point to additional free disk blocks, etc.
	- Maintain a free inode list (inode cache):
		Caches free inode. When allocate an inode, just remove it from cache and mark in the i-list not free.

3. I-list
	- For each inode, it contains: Device, Inode Number, Mode, Link Count, Owner and Group, Size, Disk Map

4. Disk map

	<img src="https://farm8.staticflickr.com/7031/26950370586_6eea21b2b0_h.jpg" width="450" height="320" alt="Disk map">

	0~9 each point to a data block, 10 point to a block which contains 256 max entries and each entry point to a data block, and 11 have one more layer and 12 have two more layer.

#### Improvement of S5FS

Rhinopias's S5SF have very low effective transfer speed. We need to improve access time (seek time + rotatinal latency + data transfer time)

- Increase block size
- Minimizing seek time
	- Use cylinder groups to put related data in the same cylinde group
- Minimizing Latency
	- Block interleaving
- Log-Structured File System (LFS)

	<img src="https://farm8.staticflickr.com/7040/26388472283_d457c0580f_b.jpg" width="400" height="100" alt="LFS">

	- Main principles: nerver delete, append only.
	- Inode Map: keep the updated inode, and each inode map piece is appended to log.
	- Use checkpoint file (can have previous and current version) which is not belong to log to keeps track of all inode map pieces.


### Multiple Disks

#### Benefits of Multiple Disks
- Hold more data
- Can be stored redundantly, increase reliability and availability
- Can spread data, allowing parallel access, increase effective access time

#### Logical Volume Manager (LVM)


<img src="https://farm8.staticflickr.com/7711/26950370436_e841701895_o.png" width="350" height="150" alt="LVM">


Manage multiple disks and try not change existing file systems much.

- Spanning: Multiple disks appear to file system as one large disk
- Mirroring: Write redundantly to multiple disks. Read from one.

#### Striping

One disk keep one striping unit data and then the next disk keep next one string unit data and keep doing this.

- Advantages: Increase parallelism.
- Disadvantages: Worse reliability, higher variance and heterogenious disks will reduce the saving access time.
- In general, performance is better with larger striping unit, which can reduce seek time
- Probability of N-disk system failing is: 1-(1-fail)^N

#### Redundant Array of Inexpensive Disks (RAID)

- level 1: Just mirroring.
- level 2: Similar to memory's ECC, use serveal disks to check bits.
- level 3: Use one disk to maintain parity bits.
- level 4: Use one disk to maintain parity blocks.
- level 5: Solve level 4's problem about write performance bottleneck that has no special disk to maintain parity blocks and spread parity blocks to all disks.


### Flash Memory

1. Technologies of Flash
	- NOR: Byte addressable.
	- NAND: Page addressable, cheaper but has limit on P/E cycle, about 10000.
2. Wear leveling
	- Writing problem: To change zeroes to ones, must erase entire block.
	- Spread writes/erasures across entire drive.
	- Using flash traslation layer(FTL) and log-structured file system.

### Crash Resiliency

#### Crash problem
 - Disk update, gather all dirty blocks, which is done one disk block at a time.
 - In S5fs and FFS, the lower level file system can sequence disk writes in any order.

#### Dealing with Crashes
1. Soft Updates
	- Consistency-preserving approach: order disk operations to preserve meta-data consistency.
	- Have problems. In reality, in order to save the numver of disk writes, multiple objects can be packed into a diks block and will make a cycle in the topological order, and breaking circular dependency will slow the preformance.

2. Journaling

	<img src="https://farm8.staticflickr.com/7251/26387400194_a85f1f718a_b.jpg" width="400" height="60" alt="journaling">

	- Use transaction idea, a journal is append-only, and it is a separate part of the disk(can add to any file system).
	- Append a commit record, when it's time to update the file system, write to journal first, and write data to file system only after the commit record is writeen to the journal.
	- Undo journaling(record previous contents), redo journaling(record new contents).
	- Recovery will take the file system into a consistent state at a transaction boundary, and copying a disk block to the file system is idempotent.

3. Shadow paging
	- Based on copy-on-write ideas

		<img src="https://farm8.staticflickr.com/7589/26387505004_aabd34527c_b.jpg" width="400" height="240" alt="shadow_paging">

### Naming and Directories

#### Directories
1. S5FS Directories
	- Each entry is 32 bytes.
2. FFS Directories:
	- No restrictions on names.
	- Maintain the last entry to get the free space.
	- Use Hash table or B+ tree to look for a file name.
	- Use extensible Hashing, the low-order i bits of hi(x) are the same in hi+1(x)
	- B+ tree, internal nodes contain no data, just keys, leaf node are linked to ease sorted sequential traversal

#### Name-Space Management
- For Windows, it use drives to make the name-space appear uniform. For Unix, it use file system mounting.

### File systems Summary

This is a review of file systems, and focus on S5FS, multiple disks, flash memory, crash Resiliency, naming and dirctories.

<img src="https://farm8.staticflickr.com/7240/26387779154_5758d2a7f6_b.jpg" width="450" height="280" alt="file_system_summary">
