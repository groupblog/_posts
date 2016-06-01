title: File System
categories: 
    - Studying Notes
    - Operating System
date: 2016-05-27
---
<img src="https://c1.staticflickr.com/8/7412/27021627480_c5303be219.jpg" width="500" height="294">
<!--more-->
----

## The Basic Of File Systems

### UNIX's S5FS

file system: manage object in secondary storage
* file system independent: virtual file system(VFS)
* file system dependent: actual file system

a file object(VFS) use an array of function pointers implement polymorphism

disk is an array of blocks of 1KB each:
* boot block
* superblock:
 * describe a free inode list(inode cache)
 * contain head of free list
* i-list: an array of inodes, each representing a file
* data region

<img src="https://c1.staticflickr.com/8/7507/27021628560_cbef10b04f.jpg" width="285" height="386">

inode contains: device, inode number, mode(file type), link count, owner, group, size, disk map

<img src="https://c3.staticflickr.com/8/7744/27021628730_c45d76fd57.jpg" width="282" height="423">

disk map:
* contains 13 entries
* first 10 entries points to 10 blocks in data region, each block is 1KB
* 11th entries has one level indirection, points to a sepcial block, which contains 256 entries pointing to 256 blocks in data region
* 12th entries has two level indirection
* 13th entries has three level indirection

<img src="https://c7.staticflickr.com/8/7367/27021628870_5b83a21a78.jpg" width="500" height="349">

free list in superblock: 
* contain 100 free block pointer,last point to a special block containing next 100 free block pointer
* if a free block is used, corresponding entry points to 0, become idle
* if 99 free block in superblock is used and need a free block, copy special block into superblock, use special free block
* if a block is freed, an idle entry points to the block
* if free list is full and a block need freed, copy entries into freed block, wipe out free list and point to freed block

<img src="https://c3.staticflickr.com/8/7289/27021629130_d9c09d160b.jpg" width="500" height="294">

inode cache in superblock:
* inode are marked free or not free
* superblock track first 100 free inode
* allocate an inode: mark it not free, remove from inode cache
* if inode cache is empty: scan i-list to refill it
* free inode: mark it free, add to inode cache if has room

<img src="https://c7.staticflickr.com/8/7289/27021629270_60759894fc.jpg" width="500" height="368">

create file:
* get a free block, update free list
* get a free inode, update i-list and inode cache

delete file:
* add block to free list
* mark inode free in i-list, may update inode cache

### Disk Architecture

disk architecture:
* smallest addressable unit is a sector
* disk address=(head/surface#, cylinder/track#, sector#)

<img src="https://c1.staticflickr.com/8/7389/26689576304_8119b21634.jpg" width="500" height="283">

### Improving Performance

access time = seek time + rotational latency + data transfer time

FFS(fast file system):
* better on-disk organization
* longer component names in directories
* retains disk map of S5FS

improve performance(FFS):
* hardware: employ pre-fetch buffer(read often in sequential)
* software:
 * increase block size:

	<img src="https://c5.staticflickr.com/8/7318/26689576324_e0803bc13a.jpg" width="500" height="297">

     * downside: worse internal fragmentation

	<img src="https://c7.staticflickr.com/8/7422/27199845262_d164a5872b.jpg" width="500" height="309">

     * solution: blocks may split into fragments that can be independently assigned to files, number of fragments per block is fixed for each file system

<img src="https://c3.staticflickr.com/8/7419/27199845322_058b548502.jpg" width="500" height="344">

 * minimize seek time:
     * keep related things close
     * separate unrelated things
     * put inode, directories and file in same cylinder group

<img src="https://c6.staticflickr.com/8/7235/26690515813_5a17e552e1.jpg" width="500" height="110">

 * reduce rotational latency: use block interleaving

<img src="https://c4.staticflickr.com/8/7292/27296651475_74bdaea7b7.jpg" width="500" height="263">

 * aggressive caching: cache all commonly used files in primary memory

<img src="https://c4.staticflickr.com/8/7709/27296651915_f979c87e2b.jpg" width="500" height="288">

aggressive caching: 
* use buffer cache(hash function keyed by inode number uesd to locate file blocks) kept in file system, most read and write have cache hit
* for write, need update disk:
 * write through: write back as soon as possible
 * write back: write back wait for a while, have risk crashing and lossing data

<img src="https://c2.staticflickr.com/8/7352/27296651945_989ff17b6d.jpg" width="500" height="228">

log-structured file system: never delete, append only(minimize seek time and rotational latency)
* if modifying file A, append A to last block, append new inode who points to A to last block, repeat until changing root inode

<img src="https://c4.staticflickr.com/8/7539/27296651995_50507e8163.jpg" width="500" height="126">

* inode map: if modifying file A, append a piece of inode map(pointing to inode to A), checkpoint file(two copies) keep track of all inode map pieces on a fixed region

<img src="https://c2.staticflickr.com/8/7476/27296652025_001c2a6d92.jpg" width="500" height="139">

* good performance for write, can recover from crashes, but waste lots of disk space

file system in windows: extents
* file is indexed in runlist(same function as inode)
* contains many length and offset

<img src="https://c8.staticflickr.com/8/7546/27296651695_c5b7109b52.jpg" width="500" height="204">

* problem:
 * lots of small areas of free space: use defragmenter to coalesce free space
 * linear search through a long list of entexts: use multiple levels

<img src="https://c6.staticflickr.com/8/7324/27296651765_e5cd9da946.jpg" width="500" height="340">

## Crash Resiliency

dirty/modified blocks: disk block in buffer cache is dirty if modified, file system periodically gather dirty block, update disk

updating disk:
* S5FS&FFS: file system can sequence disk write in any order
* soft-update: provide recoverable consistency
* journaling and shadow paging provide: provide transactional consistency

<img src="https://c6.staticflickr.com/8/7306/27296652085_37f0c5e392.jpg" width="500" height="276">

soft update:
* order disk operation to preserve meta-data consistency, update cache and disk

<img src="https://c6.staticflickr.com/8/7014/27296652165_38f7dfca46.jpg" width="500" height="198">

* could have innocuous inconsistency, need to reclaim lost disk block

<img src="https://c8.staticflickr.com/8/7441/27296652215_9f4f5f7f3f.jpg" width="466" height="365">

* doesn't work if disk operation don't have topological order

<img src="https://c8.staticflickr.com/8/7528/27296652295_e72a35d325.jpg" width="500" height="212">

transactions:
* group disk write into transactions
* ACID property: atomic, consistent, isolated, durable
* journaling: before updating disk
 * undo journaling: "before images" of blocks are writeen into journal
 * redo journaling: "after images" of blocks are written into journal
* shadow paging: steps of transactioin written to disk, old values remain, single write switch old state to new

journaling:
* journal is separate part of disk
* journal is append-only, have commit record one block in size
* when updating file system, write to journal first, then write to file system after writing commit record to journal

<img src="https://c8.staticflickr.com/8/7336/27296652375_cc673fa2bf.jpg" width="500" height="73">

journaling recovery:
* make file system in consistent state at transaction boundary
* find all committed transactions
* redo all transactions
* after recovery, state of file system is at the end of last committed transaction

journaling option:
* journal everything
* journal metadata

journaling vs log-structured file system
* log-structured file system: good write performance, recovery using checkpoint file
* journaling: crash resiliency, can add to any file system, use checkpointing to clear journal

shadow paging: based on copy-on-write

<img src="https://c1.staticflickr.com/8/7551/27199846712_faba88c8a0.jpg" width="500" height="299">

## Directories and Naming

FFS directory format: inode number, entry length, name length, name(any length is acceptable)

<img src="https://c7.staticflickr.com/8/7289/27199846782_b7d4e6d329.jpg" width="430" height="500">

lookup filename in directory:
* linear search: O(n)
* B+ tree: O(logn), all leaves appear at same level and carry no keys, internal nodes contain no data, leaf nodes are linked

<img src="https://c1.staticflickr.com/8/7452/27199846992_4c8779a5c7.jpg" width="500" height="329">

* hash table: O(1), extensible hashing, one level indirection

<img src="https://c5.staticflickr.com/8/7724/27199846852_cba250caca.jpg" width="500" height="270">

name-space management: when having multiple partitions of hard drive, use mounting to creaet an entry in inode pointing to file system, happens in memory

<img src="https://c7.staticflickr.com/8/7426/27199847062_89881893f9.jpg" width="500" height="360">

## Multiple Disks

advantage of multiple disk:
* hold more data
* stored redundantly to increase reliability and availability
* allowing parallel acess to reduce access time

logical volume manager(LVM):

<img src="https://c5.staticflickr.com/8/7393/27199847132_10b0517b10.jpg" width="500" height="353">

* spanning: multiple disks appear to file system as one
* mirroring: file system write redundantly to multiple disk
* striping: store part of file in each disk
 * stripe width: the number of disks to write to
 * striping unit: the size of file to write to each disk

<img src="https://c5.staticflickr.com/8/7008/27199847332_b788d522dd.jpg" width="500" height="256">

parallel disk:
* advantage:
 * retrieve blocks belonging to multiple files simultaneously
 * reduce access time when block is spread over multiple disk
* disadvantage:
 * higher variance
 * worse reliability
 * heterogenious disk

RAID(redundant array of inexpensive disks): combine striping with mirroring
* level 1: just mirroring

<img src="https://c1.staticflickr.com/8/7325/27021628480_709f1bf59a.jpg" width="500" height="314">

* level 2: data plus ECC(check every bits, need serveal disks)

<img src="https://c7.staticflickr.com/8/7791/27021628310_dbb0a0fbe9.jpg" width="500" height="374">

* level 3: data plus parity(check sum of bits, need one disk)

<img src="https://c7.staticflickr.com/8/7702/27021628150_4927ff211e.jpg" width="500" height="372">

* level 4: data  plus parity(check sum of block, need one disk), bottlenect at parity block

<img src="https://c5.staticflickr.com/8/7409/27021627980_8d5000ef58.jpg" width="500" height="371">

* level 5: level 4 plus spread parity block

<img src="https://c1.staticflickr.com/8/7073/27021627880_76e4be0f22.jpg" width="500" height="254">

## Flash Memory

flash memory:
* advantage: use block to store data, random access, no seek, rotational latency, low power, vibration-resistant
* disadvantage: limited lifetime, cost more than disk, write is expensive(change from 0 to 1 whole block)
* NOR(byte addressable), NAND(page addressable)

wear leveling: spread write across entire drive
* use flash translation layer(FTL): map disk block to flash block
* use log-structured file system

<img src="https://c5.staticflickr.com/8/7318/27021627740_ff10b2f5d9.jpg" width="500" height="119">