# lab2 answer

## Question
Q1:Assuming that the following JOS kernel code is correct, what type should variable x have, uintptr_t or physaddr_t?

A1: uintptr_t

Q2:What entries (rows) in the page directory have been filled in at this point? What addresses do they map and where do they point? In other words, fill out this table as much as possible:

A2:

|Entry|Base Virtual Address|Points to (logically)|
|-|-|-|
|1023|0xffc00000|Page table for remapped whole physical memory|
|...|...|Page table for remapped whole physical memory|
|960|0xf0000000|Page table for remapped whole physical memory|
|959|0xef7c0000|CPU Kernel Stack|
|958|0xef800000|?|
|957|0xef400000|Current page table|
|956|0xef000000|READ-ONLY PAGES|
|...|...|?|
|1|0x00400000|?|
|0|0x00000000|?|

Q3:(From Lecture 3) We have placed the kernel and user environment in the same address space. Why will user programs not be able to read or write the kernel's memory? What specific mechanisms protect the kernel memory?

A3:用户没有权限访问PTE_U置零的页表。

Q4:What is the maximum amount of physical memory that this operating system can support? Why?

A4:256M JOS needs to map all physical memory at virtual memory

Q5:How much space overhead is there for managing memory, if we actually had the maximum amount of physical memory? How is this overhead broken down?

A5:
page directory:1 page = 4 KB
page table = (4KB/4 byte)page = 4MB
pages 32768*16byte = 512KB
如果使用大页，pagetable的空间会节省下来，但是可能有浪费。

Q6:Revisit the page table setup in kern/entry.S and kern/entrypgdir.c. Immediately after we turn on paging, EIP is still a low number (a little over 1MB). At what point do we transition to running at an EIP above KERNBASE? What makes it possible for us to continue executing at a low EIP between when we enable paging and when we begin running at an EIP above KERNBASE? Why is this transition necessary?

A6：
在~kern/entrypgdir.c 里，把VA的[0, 4MB)和[KERNBASE, KERNBASE+4MB)映射到了PA的[0, 4MB)，paging使用前后，映射的都是同一块物理空间，所以可以连续执行。


## Challenge

### showmappings

```
Usage: showmappings [vastart] [vaend]  show all maps in range [vastart, vaend)
```
```
K> showmapping 0xf0000010 0xf0800001
0xf0000000-0xf03fffff -> 0x00000000-0x003fffff 4M rws
0xf0400000-0xf07fffff -> 0x00400000-0x007fffff 4M rws
0xf0800000-0xf0bfffff -> 0x00800000-0x00bfffff 4M rws
```

### change permission

```
Usage: changepgperm [addr] [u|s|r|w]

```

```
K> showmapping 0xf0000000
0xf0000000-0xf03fffff -> 0x00000000-0x003fffff 4M rws
K> changepgperm 0xf0000000 u
K> showmappings 0xf0000000
0xf0000000-0xf03fffff -> 0x00000000-0x003fffff 4M rwu
K> changepgperm 0xf0000000 r
K> showmappings 0xf0000000
0xf0000000-0xf03fffff -> 0x00000000-0x003fffff 4M r-u
```

### dump
```
usage: dump [v/p] [addr] [range]
```
```
K> dump v 0xf0000000 20
53 ff 00 f0 53 ff 00 f0 c3 e2 00 f0 53 ff 00 f0 53 ff 00 f0

K> dump p 0 20
53 ff 00 f0 53 ff 00 f0 c3 e2 00 f0 53 ff 00 f0 53 ff 00 f0
```
