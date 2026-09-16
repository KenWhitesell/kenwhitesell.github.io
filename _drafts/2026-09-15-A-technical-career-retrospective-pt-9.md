---
layout: post
title: A technical career retrospective part 9
subtitle: 'Atari ST: 1985 - 1991'
tags: Personal
---

### Why this post?

My hobby takes a more "business-oriented" turn. My focus changes from general research, entertainment and mathematics to more "data-oriented" uses.

### TL;DR

I enter the 32-bit world at home.

### Background

By 1983, the small-computer market had completely changed. The introduction of the IBM PC in 1981 set new standards for CPU and memory usage. In 1983, IBM released the PC-XT with an internal hard drive (10MB!) and the ability to put 640K RAM on the motherboard.
But a well-stocked IBM XT would have been about $5000.

The clones were starting to become available in 1982, generally at half the price of the IBM models - but we're still talking about $2500 for a reasonably-equipped system.

At work, I had the opportunity to use a Zenith H-100. This system was rather unique in that it had both an 8085 _and_ an 8088 CPU in it. You could either boot CP/M, using the 8085, or you could boot their version of MS-DOS called Z-DOS to run 16-bit applications. It was a very powerful system, but also cost about $5000 when fully equipped.

The Apple Macintosh was released in 1984. Yes, I remember seeing that one time that the original Mac 1984 ad was broadcast during the Super Bowl. I was very excited - until I had the opportunity to play with one for a few minutes.

Sure, the 128K memory was twice what my Apple ][ could use - but for what it was trying to do, it was too small. Also, the screen resolution (512x342) was insufficent for working comfortably on 80 columns of text, which was still pretty much standard.

Finally, in order to maximize the usable space on a diskette, Apple used a variable-speed drive. This drive allowed for more data (more sectors per track) on the outer tracks, but reduced the effective speed of the drive due to needing to change the speed of the drive whenever the heads crossed one of the speed-change boundaries.

The 68000 CPU was amazing, but limited memory and slow diskette drives made it painful to use.

In September '84, the "Fat Mac" (512K version) became available - but cost more than $3000. Still out of reach financially, and still the same small screen.

In January 1985, Atari announced their Atari 520ST. A 512K 68000-based system with an external floppy drive and a 640x400 monochrome monitor for $800, or about a quarter of the price of a comparable Mac.

This made my choice a no-brainer. When they were first released in April, I got on the waiting list. I also registered to get the Atari ST Developer's Kit ($300), because I knew that I wanted to do things with it that probably wasn't going to be available for a while.

### First impressions

Wow! I'm now one of the first people in my area to own one. There's 512K of memory, 360K diskettes. An actual monitor - not something I'm hooking up to a TV.

Along with that, my copy of the developer's kit arrived. It consisted of 5(?) diskettes and about 1500 3-hole punch pages of documentation.

Buy some binders, organize the docs, start browsing.

Power up.

Wait. 

What? The OS isn't in ROM? 

You need to boot the OS from diskette? OK, ROMs aren't ready yet. 

How much space does it need? 200K +? Ok, that still leaves me with 300K usable.

Well no, if you load any desktop accessories, they consume memory. The screen itself uses 32K.

In practice, a usable system would leave you with about 256K. Still a considerable amount, but no longer enough to store an entire diskette in memory.

At least you could remove the boot disk once it had loaded. Everything was in memory, so there was no need to keep the disk in the drive.

Why does this matter?

The first thing you wanted to do was to make backups of your diskettes - especially your boot disks. Without a boot disk, the system was useless.

Copying disks was a tedious process - it took about 5 minutes to duplicate a 360K diskette with only one drive, because you had to swap diskettes multiple times. And my experiences with floppy diskettes was such that I was never satisfied with only one backup copy. I made two backup copies of every important disk (the developer's kit) and three of critical disks (the boot and language disks).

The 3.5" diskettes at the time were about $50 (retail) for a box of 10. Two boxes for backups was another $100.

I realized quickly that having one drive was going to be too much of a bottleneck. (I should have remembered that from my early Apple 2 experience.) There went another $200 for a second drive.

### Getting started

Compiling a program using the development kit was tedious. The C compiler was a 3-pass compiler, assembler and a linker, which meant five separate programs were run for any single-file program.

Having your program divided into smaller modules was worse. The three compiler steps and assembler had to be run for each module, and then the compiled modules had to be linked together to make the program file.

There was no shell. No command-line facility at all. You could create a file containing a sequence of commands to execute as a "batch" file (`.bat`) and run it. But there was no way to do that interactively.

And, there was no "make" facility. If you had a program consisting of 5 separate source files, your batch file to do your build would run the full compile set for all 5 before linking them together.

With all the files on diskettes, there was a lot of head movement between the compiler steps, reading the source and header files, and writing the intermediate files. Once you got past the "toy program" phase, compiling a source file required at least 5 minutes. If you've got your program divided into 8 separate source modules, a full build could take more than an hour.

I gave up. There was no way I was going to be able to create anything of substance in this environment. 

Don't misunderstand, I was still quite happy with the ST. The supplied word processor (ST Writer) and terminal program (VT-52 Emulator) allowed me to get a lot of personal value from my purchase. And, I knew that once the TOS ROMs were shipped, I would have a couple of different ways that performance would be improved.

### Everything changes for the better in 1986

By July 1986, one year later, many things had changed for the better.

- On the personal side, I was discharged from the Air Force in December 1985 and got a job as a programmer in 1986 for almost twice the pay that I was getting in the military. That greatly eased my budgetary constraints. (I went from about $14,000 per year to $26,500.)

- The Atari TOS ROMs were available and I had them installed. (That immediately doubled the amount of usable memory in the system.)

- The new TOS ROMs supported the new double-sided diskettes, increasing disk capacity from 360K to 720K. And, they were effectively faster in that twice as much data could be read or written before needing to move the heads. So I replaced one of my drives with a double-sided drive.

- It was discovered that an Atari 520 ST could be upgraded from 512K to 1 MB by piggy-backing a second set of memory chips to the top of the existing set - with the exception of two pins that had to be wired to a particular place on the motherboard.

This seemed a bit "iffy" to me at first, but once I talked to two other people who had it done - and were quite happy with the results - I went ahead with it.

One of the real advantages of the memory upgrade is that it allowed me to create a 384KB RAM Disk. The compilers, assembler, linker and system libraries would all be able to fit on it - greatly increasing the speed.

Everything else that was needed fit on one diskette. A complete compilation process could be done without switching diskettes.

What used to be a 25-30 minute process would frequently be done in a couple of minutes.

But the irony here is that other software was being published that eliminated my needs for doing much C-language programming for myself.

- Commercial quality software was becoming available. VIP Professional was the first really serious spreadsheet, and dBMAN was an excellent database system. 1st Word was bundled with early STs, making it the default word processor in many areas. Finally, Flash, a fully-scriptable was released

I could now do the real things I wanted to do without having to write a lot of custom code for it.

This configuration served me well for about a year and a half. The material I was producing was either being sent electronically or printed on my dot-matrix printer.

### A massive upgrade in 1988

In late '87 / early '88, I bought an Atari Mega ST 4, which was the 4 MB version of the ST. I also bought the Atari SLM804 Laser printer, the desktop publishing program Calamus, and a 100MB hard drive. There were also some greatly improved development tools available, include a couple of command-line shells and utilities that brought Unix-style scripting to the ST.

The SLM804 was unique - it didn't have any processor or memory in the printer. Everything was driven and controlled by the Mega ST. Whatever software you used had to allocate a full megabyte of memory to build the page image as a bit map in the computer's memory. The computer would then run a very tight loop to send data at the rate the printer could accept. You couldn't use the computer at all while pages were being printed.

This design greatly reduced the printer's cost, being sold "bare" (no software) for about $1500. Add some fonts and other software and you could get the bundle for less than $2000. Meanwhile, the HP LaserJet II was being sold for about $2500 at that time.

From my perspective, this gave me a near-professional setup. 

At one point in time, I had a script set up to help me run a Fantasy Baseball League:
- Dial in to an online service (Computer Sports World) to retrieve data (Flash)
- Process the data, generating text files (dBMAN)
- Create a "text page" version suitable for sending through email (shell script)
- Create a newsletter style layout of the output, suitable for printing (Calamus)

I would run that, and then add whatever commentary information desired as text to that layout, and print it on the laser printer.

I also found myself at a later time editing and printing newsletters for a couple of small groups I belonged to.


### STacy

Early in 1990, I bought a STacy 4. It was an Atari laptop with 4 GB RAM and an internal 40 GB hard drive. The original system was intended to be run on 12(!) standard C-cell batteries, but that only provided about 15 minutes of use.

A friend of mine helped develop a ni-cad battery pack that was built into a laptop case. The base of the case was a complete layer of batteries, giving somewhere between 6 and 8 hours of operating time.

The case, batteries, and other electronics brought the total weight to about 25 pounds.In practice, this made it more of a "transportable" computer than a real laptop, but it was still more convenient to take on the road than a full ST system.


### Sunset

That was an incredible 3 years for me, but the writing was on the wall. The rapid growth and development in the IBM compatible world, especially with the release of Windows 3.0 for the 80386, made me realize there wasn't a long-term future in the platform.

It wasn't just CPU speed or memory capacity causing me to become dissatified. Video resolutions were better - standard VGA at 640x480 at 16 colors was visually superior to anything the ST could do. But by 1991, the high-end standard was XGA (1024x768 at 256 colors), combined with 14" to 19" monitors. (These were configurations I was using in my day job, )

I was also missing the ability to expand the system using plug-in cards in slots.

In 1991 I put together a 33 MHz 80486 system with 16 MB RAM and another 100 MB hard drive.

Somewhere around 1992, I sold off whatever Atari ST gear I had left at whatever price I could get for it.
