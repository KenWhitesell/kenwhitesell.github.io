---
layout: post
title: A technical career retrospective part 10
subtitle: 'IBM MVS: 1986 - 1988'
tags: Personal
---

### Why this post?

I started with the Monumental Life Insurance Company in June 1986 in their programming department. My first exposure to a real batch-oriented production system environment!

I covered a lot of the MVS fundamentals back in [part 7]({% link _posts/2026-08-18-A-technical-career-retrospective-pt-7.md %}) from a user-facing perspective. 

The management of datasets in MVS was unique to me. I have never worked on any other platform that required so much specific technical knowledge when working with data being stored on hard drives. Every other system I've used had completely abstracted away the physical geometry, making it invisible to the application developer.

### TL;DR

I learned a lot more about MVS this time around, giving me an even deeper understanding of the mainframe environment.

### Disclaimer

IBM z/OS is still currently a very active system. It has also made tremendous advancements in the past 40+ years. A lot of what I describe here may have been true in 1986 but isn't true anymore. Don't take what I say about "MVS - then" to be relevent when talking about "z/OS - today".

### DASD allocation

#### Block size

The first truly eye-opening issue I learned was the importance of calculating block sizes for datasets. The IBM system did not use a standard or fixed block size on disks or tape. A track on a 3380 DASD unit could hold 47,476 bytes.

This was quite a conceptual change from the Apple using a 256 byte sector size, the Honeywell and its 64 word sectors, and the MS-DOS/Atari ST diskette 512 byte sectors.

The maximum standard block size for most MVS software was 32,760 bytes. But if a dataset was created with that block size there would only be one block per track, wasting roughly 30% of the allocated space.

Each block on a track also incurrs some degree of overhead for the physical gap between the blocks and the block headers.

Performance is also affected by the number of I/O requests needed to read any given amount of data. Each I/O request creates a sizeable amount of overhead for the OS to schedule and execute that request. That overhead was significantly longer than the time required for the disk to spin past the start of the next block, requiring the controller to wait a full rotation before being able to read that next block. 

A dataset having 60 80-byte blocks on a track requires the disk complete 60 rotations for the data to be read, which is one full second of time. If those 80-byte blocks are read as one 4800-byte, that entire block is read as one I/O operation in one rotation of the disk, making it 60-times faster.

The general key to optimizing performance and space utilization was to find the largest block size that was a multiple of the block size less than half the usable track size, which worked out to 23,476 bytes. (That's less than half of 47,476 because of the additional overhead of the second block on the track.)

For example, with 80-byte records, a 23,440 byte block holds 293 records and two blocks use more than 98% of the disk track capacity. If that is reduced to an 80-byte block, only 411 blocks will fit on a track using just 32,880 bytes. This uses less than 70% of the space on the disk. 

These differences are more significant with magnetic tape. In the early 80's, the common tape reel was 2,400 feet long using a density of 6,250 bits per inch (BPI). A tape using a 32,400 byte block size could contain about 170 MB. If the tape is written with 80-byte blocks, the tape would hold less than 3.5 MB. The gap between blocks is actually 25 **times** as long as the data block itself. (The standard gap for 6250 BPI tape drives is 0.3 inches, less than half the gap of the old 1600 BPI standard.)

#### Space allocation

Another performance factor with datasets is that they were allocated in contiguous space as much as possible. 

Space allocation on MVS was measured in terms of tracks or cylinders. Each track on an IBM 3380 DASD unit was 47,476 bytes. How that track was divided into blocks was up to the developer.

The allocation is defined as two numbers, a primary allocation and a secondary.

If a dataset was configured to use 10 cylinders as the primary, MVS would attempt to find 10 free contiguous cylinders. This would minimize the amount of head movement needed to read or write that dataset.

If there weren't 10 contiguous free cylinders, MVS would try to find up to 5 non-contiguous chunks to satisfy the allocation. (These chunks are known as extents.)

The secondary value is the amount to add if the dataset grows beyond its current size.

Each additional allocation is another extent.

The fewer the number of extents, the fewer the number of times the heads had to move to a non-adjacent cylinder. Moving to an adjacent cylinder was about 3 ms. An average non-adjacent move took 5 times as long, so there was a definite benefit to reducing the amount of head movement necessary. 

A dataset may not have more than 16 extents total. Tracking the number of extents used by a dataset and its rate of growth was an important part of managing your datasets.

MVS also allowed specifying the number of blocks to allocate, allowing it to calculate the amount of space needed. This was preferred in some areas having different model DASD units to avoid making the JCL dependent upon a specific unit. (We were encouraged to)

#### Volume allocation

Finally, when working on a DASD-intensive process, it was important to spread the datasets around to not create a bottleneck on a particular volume. If a process is reading both a master file and a set of updates and creating a new instance of the master file, the process would usually run much more quickly if those three datasets were on different volumes.

If there's a lot of batch processing occurring overnight, it could be critical to coordinate work being performed by those different processes to reduce conflicts regarding DASD volume utilization. Situations where different batch jobs are both heavily relying on the same volume at the same time should also be avoided.

### Dataset organization

Processing sequential files was the only way of managing data when the only common mass-storage media were punch cards and magnetic tape. 

The DASD units provided the ability to access data randomly. Information could be read or updated without reading an entire file. This improved performance for processes that only needed to update a small percentage of the data.

Before databases became popular, there were two different types of files typically used to take advantage of this. The first was known as the Relative Record Data Set (RRDS). This only worked with fixed-sized records, but allowed the programmer to directly access any record based upon its record number.

The other, more intricate organization was the Key Sequenced Data Set (KSDS). A KSDS consisted of both a data cluster and an index cluster. The records were defined with some part of that record being the unique primary key. The index cluster maps that primary key to the location of that record within the data cluster. 

This provided the ability to either read the file sequentially or by retrieving a record based upon its key. Records could also be inserted at any location, provided the key provided was unique.

A common technique was to identify the primary key but add a suffix to it to allow for future expansion. The precise semantics of that suffix could vary based upon need. It could be a record type or a sequential number - or even be a combination of the two.

It was also common to mix the two reference types. A program could randomly retrieve the first record for a primary key, and then read sequentially through the set of records to process all the different suffixes that exist on the base key.

It was also possible to define alternate indexes (AIX). Those indexes were not required to be unique. For example, it was possible to create an AIX on zip code if it were necessary to organize your processes based upon it.

Monitoring KSDS performance was an ongoing requirement. There was a lot of internal space management necessary on active datasets that I've glossed over. Highly active datasets would need to be reorganized periodically to keep performance at acceptable levels.

### Application programmer's perspective

These details weren't important to the COBOL code written to process those datasets. That was an important feature of the MVS operating system. The code would be the same regardless of the block size. 

Where this mattered was in the construction of the JCL used to manage the job and the datasets being used.

### Comments on COBOL

COBOL is frequently looked down-upon for a number of mostly-valid reasons. I won't deny that it can be a frustrating language to deal with under a variety of circumstances.

But it also had some very powerful features:
- Intrinisic support for numbers being stored in multiple format. It didn't matter if a number was stored as a sequence of characters (1 digit per byte), packed decimal (2 digits per byte), binary integer (2, 4 or 8 bytes), or floating point (4 or 8 bytes). The same operations could be performed on all of them.
- A hierarchical structured data definition. Data elements are generally grouped into a higher level structure. Cobol allows most operations to be performed on either an individual field or group.
- Move corresponding. A program could move a set of elements from a source to a destination based upon the names of those elements. This was particularly useful for reorganizing data fields when creating reports. 
- Array bounds checking. A compiler option was available to force array indexes to be checked to ensure the memory reference was within the bounds of the table being accessed.

### Dylakor

We had another language available to us called Dylakor. This was an interpreted language that was ideal for quickly producing reports. It used a greatly-simplified syntax for describing data formats, and had the advantage that a complete record did not need to be defined. If only three fields from a large record were needed, the record definition would only contain those three fields by identifying their offset from the beginning of the record.

It also had an implicit processing loop. In the simplest case, the entire script would be run once for each record in the input dataset.

### Memory limits

One earlier architectural limitation of the IBM systems was that internal addresses for a process was limited to 16 MB. (It used 24-bit addresses.) The physical system could have more than that, but each process could only reference 16 MB. That 16 MB boundary became known as "the line".

As software and data requirements grew, this became a more significant issue. In 1983, IBM released hardware and software upgrades that allowed for 31-bit addresses, expanding the addressible memory to 2 GB. Code using 31-bit addresses internally was said to be able to run "above the line".

Why a 31-bit address instead of a 32-bit address? IBM decided that the easiest way to maintain compatibility with older software was to use that first bit as a flag to indicate the type of address being used. If bit 0 == 0, then the address is a 24-bit address and the other 7 bits of that byte would be ignored. If bit 0 == 1, then the address is a 31-bit address, and the other 7 bits of that byte are part of the address.

But why was it necessary to ignore that first byte? Again, it's a historical artifact from back when memory was extremely tight. Developers learned to use that first address byte for other purposes. It was not uncommon to use them for internal status indicators within the code. In those situations, that first byte would not necessarily be 0, creating an error if those bits were used as part of the address.

In the mid-80s it was necessary to manage software modules using a combination of 24 and 31 bit modes. It was possible to have code that could only run below the line access data that was above the line. That was one of the easiest changes to make, and moving I/O buffers above the line was a quick win in a number of situations.

