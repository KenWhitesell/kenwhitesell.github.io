---
layout: post
title: A technical career retrospective part 14
subtitle: 'PC Support part 3: 1988 - 1995'
tags: Personal
---

### Why this post?

Once the PCs were networked, there came the desire to build and use software to take advantage of them.

While the majority of people only used the network for file and print sharing and for mainframe access, some groups had more collaborative ideas.

### TL;DR

We didn't just do harware - software was part of the picture as well.

<!--more-->

### Disclaimer

Same disclaimer as before - I can't list these in any specific order.

### Lotus 1-2-3 "bridge"

One of the first PC-based programming projects I remember being involved with was the creation of a specific file-format conversion process. 

The actuarial department used proprietary software for performing certain modeling projects. They would run multiple iterations of this software making minor changes to key parameters for each test. However, changing these parameters required the user to use the software's menu system to make these changes.

They wanted a tool that would let them build these various settings in Lotus 1-2-3, export that to a text (csv?) file, and run something that would create the settings files for the software to use. 

The idea was to be able to set up "X" number of tests and let them run without manual intervention. (e.g. Start them on a Friday afternoon and let the PC work on them all weekend.)

The proprietary software provided sufficient documentation for how to programmaticaly alter the data files, and to trigger a run. But I knew it wasn't practical to expect me to learn enough quickly enough to write this myself.

We hired a contractor to do the bulk of the work. My role primarily was to serve as the interface between the actuarial department and the contractor.

Things progressed rather smoothly - the program was written and tested successfully. However, when the first large-scale test was run, it ran out of memory.

Debugging revealed a memory leak in the proprietary program's library for allocating variables. Fixing it directly would have involved adding a lot of internal housekeeping to our program. I came up with a "brute-force, smash it with a brick" solution - find the beginning of the data blocks allocated by the library and reinitialize the entire block. This discarded all the individual blocks while maintaining all the internal data being used by the program. Surprisingly enough (to me) this actually worked quite well.

I believe the program went on to be used until the proprietary software was replaced.
