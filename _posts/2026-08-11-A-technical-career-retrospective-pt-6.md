---
layout: post
title: A technical career retrospective part 6
subtitle: 'Multics : 1984 - 1985'
tags: Personal
---

### Why this post?

The time frame of this post, and the next, overlap part 5. These two parts address what I was working on professionally as compared to my hobbyist efforts.

Quite honestly, I don't remember the exact sequence of events between parts 6 and 7. Chronologically, both overlapped over the same 15-month window, but I don't have anything left to remind me of the specific dates involved.

### TL;DR

My experiences with Multics - the precursor and progenitor of Unix

### Disclaimer

This isn't intended to be a dissertation on Multics architecture, but more of my recollection and perception of the Multics system I used.

Take any technical details I write here with a large bucket of salt! I didn't work with Multics long enough for most of what I learned to stick with me across 40+ years. I'm more confident about some parts of this than others - but I could simply be more confidently wrong. In other places I'm greatly simplifying the description because I either didn't know or have forgotten the details.
<!--more-->

### My introduction to Multics

Multics was yet another completely unique environment for me. It was oriented toward interactive use, similar to MTS. (About 25 years after this, I learned that the different individuals involved in designing Multics and MTS had attended conferences together to discuss some practical applications of virtual-memory based systems, and so there was some cross-pollination of ideas between the two.)

#### Another Honeywell product

The Multics we used was run on a Honeywell DPS-8 system, which architecturally was almost identical to the Honeywell 6000 system used for GCOS. The primary difference between the two was that the DPS-8 fully supported a virtual memory environment. (There may have been other differences, but I never learned enough about the hardware being used to find out what they were.)

#### Ring security

The previous mainframe systems I had worked with (IBM 370 series, Honeywell 6000) had two levels of security in the CPU, the protected state ("Master mode" on GCOS, "Supervisor state" in MTS) and the normal user mode. (Interestingly enough, the "user" mode in the IBM mainframe world is called the "Problem state" - not because it is a problem, but because that's where the business problems are solved.)

<figure style="float:left; margin:5px; padding:3px; border:2px solid black;" >
<figcaption>Very simplified ring structure.<br>Multics actually has rings 0 - 7, but typically uses 0 -4</figcaption>
<img src="/images/tech_6/multics_rings.png" width="400" height="400">
</figure>
Multics introduced the concept of security as a set of rings, numbered 0-7. However, Multics typically only used 4 rings. Ring 0, the innermost ring, was the most privileged state. The outermost ring commonly used was ring 4, used for most applications. The rings in-between provided a layered security structure, controlling access to inner layers.

Each process defines which rings can call it. If a process allows a lesser-privileged (higher number) ring to call it, it's called a gate - allowing that process to enter a more privileged ring. That gate controls access to an even more-privileged ring. Fundamentally, that gate function can do things like verify parameters before calling the even-more secured function.

For example, an application may need to read from an operating system buffer. The operating system, instead of granting read access directly to the application in ring 4, only provides access to ring 1. There might be multiple processes running in ring 1 that can access that buffer, perhaps one that validates the parameters and one that doesn't - or perhaps applies different validation rules. The process that validates the parameters might be directly callable from ring 4, while the other restricts access to ring 2 processes. This avoids the overhead of parameter validation for things like trusted device drivers, but enforces those limitations for regular user programs.
<div style="clear: left;"></div>

This layered model provides a very granular mechanism for managing access to system resources. This concept still continues today within the x86 series of CPUs, even though most OSs don't use it.

#### PL/I

The bulk of the operating system, and all our applications running on it, were all written in PL/I. (The `I` represents a Roman numeral `1`, so it's pronounced `P` `L` `one`, not `P` `L` `eye`. As a result, it's sometimes written as PL/1.)

This made it the first operating system I worked with written in something other than assembler.

<div style="float:right; margin:5px; padding:3px; border:2px solid black;" >
Simple PL/I program to add two numbers:<hr style="margin-top:3px; margin-bottom:3px;">
<pre style="padding:3px; margin-bottom:2px;">
add_constants: procedure;

  declare ioa_ entry options(variable);

  declare NUM1 fixed bin value(150);
  declare NUM2 fixed bin value(350);

  declare answer fixed bin;

  answer = NUM1 + NUM2;
  call ioa_("The total sum is: ^d^/", answer);

end add_constants;
</pre>
</div>

PL/I was an attempt by IBM to merge the uses and functionality of both Fortran and COBOL into one language. I always felt that it failed because it made too many compromises to be considered better than either of the other two languages. It felt too wordy and not as fast when compared to Fortran and not expressive enough compared to COBOL.

Also at the time, it was generally acknowledged that no one - not even IBM - shipped a fully-functional, fully-standard-compliant compiler. IBM designed the language to do just about everything, and then settled on shipping a compiler that omitted rarely-used features.

The Multics PL/I compiler was also a subset of the standard language, but did provide enough features to make it suitable for OS-level work.
<div style="clear: right;"></div>

However, my work on the system was a maintenance task to help support one specific application. I received enough training to gain a general understanding of how Multics worked, but never really got into the guts of it.

#### Hierarchical file system

Multics was my first encounter with a hierarchical file system. Everything before that was "flat", either organized by user name or physical disk. The greater-than symbol (`>`) was used as the path separator.

It also provided aliases and symbolic links. An alias was a different name for a file, similar but not identical to a Unix/Linux hard link. Symbolic links are the same as in Unix, special directory entries containing the relative or absolute path of the target file.

#### Search path

When looking for a program file, Multics had a particular search method to find the file on the system.

First, it would look to see if the file was already loaded for that process. This prevents having to search the hard drives if the code has already been used in that session.

Second, it would look through the directory containing the calling program. This ensures that a program will find any modules its packaged with.

Third, it will search your current working directory.

Fourth, it will search through the standard system libraries.

This search pattern is dynamic and operates on a per-process basis. This means that two different programs running from different directories could each call their own version of identically named modules, provided those modules were in the same directory as the program being run.

The search pattern could also be altered, either manually or programmatically, allowing for modules to be tested in a development environment without having a complete copy of the modifications being tested.

#### Memory-mapped I/O

Most interesting to me is that disk I/O was generally performed by mapping the file to the address space of the program. Instead of issuing commands like `read` or `write`, opening a file would cause the disk segments to be mapped to addresses, and reading the file would then be performed by accessing those memory locations. A PL/I data structure would be created to access the data in memory - there was no need to code specific read/write operations, and data could be updated "in place".

If the data wasn't currently in physical memory, a reference to it would cause a page fault, and Multics would swap that page in from the file. (This was a concept later implemented in Unix/Linux as `mmap`.)

This feature was used for both programs and data. A compiled program could be loaded at runtime by a different program, and a routine in that file could be called at the address assigned.

#### Segmented files

With disk files generally used as memory-mapped pages, there was an upper limit to how large those files could be. Since memory segments within a Multics system were limited to 256K 4-byte words, individual files had an effecive maximum size of 1 MB.

When files need to grow beyond 1 MB, the file became a "multisegment file". The file name became a directory name, and the individual files within that directory were the segments for the file. This was mostly transparent to the application developer - at least I never had to worry about it.

However, when the data exceeded the total number of segments that were allowed (about 1 GB), then the system would try to create a multilevel multisegmented file - and that's where things would break. A segment file would be converted to a directory, and the program reading the data wouldn't recognize this had happened. So instead of reading the desired data, it was trying to read this nested segment list as data - and fail. (My memory of the specifics here are very hazy, and probably simplified. Any true Multician that worked on that system at the time could probably give a much better explanation.)

I never really got to know the system well enough to understand what exactly was failing, just that when the system died, this was the cause. My role was primarily to help clean up the mess when it happened by shrinking the data back down to an acceptable size. Whatever actual programming done was quite minimal.

### Footnote

Like many other systems from that time, Multics lives on in the hearts and minds of the retrocomputing enthusiasts. You can run the last released version of Multics using the [DPS8M Simulator](https://dps8m.gitlab.io/dps8m/). There's more information at the [Multics WIKI](https://multics-wiki.swenson.org/).
