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

In January 1985, Atari announced their Atari 520ST. A 512K 68000-based system with an external floppy drive and a 640x400 monochrome monitor for $800, or about a quarter of the price of a comparable Mac.

This was going to be the first time for me to join the "leading edge" of a computer platform.

### First impressions

<figure style="float:left; margin:5px; padding:3px; border:2px solid black;" >
<img src="/images/tech_9/ST with monitor-small.png" width="342" height="192">
</figure>
Wow! I'm now one of the first people in my area to own one. There's 512K of memory, 360K diskettes. An actual monitor - not something I'm hooking up to a TV.

Along with that, my copy of the developer's kit arrived. It consisted of 5(?) diskettes and about 1500 3-hole punch pages of documentation.

Buy some binders, organize the docs, start browsing.
<div style="clear: left;"></div>

Power up.

Wait. 

What? The OS isn't in ROM? 

I need to boot the OS from diskette? OK, ROMs aren't ready yet. 

How much space does it need? 200K +? Ok, that still leaves me with 300K usable.

Well no, any desktop accessories being loaded consumed memory. The screen itself uses 32K.

In practice, a usable system had about 225K of free memory. Still a considerable amount, but no longer enough to store an entire diskette in memory.

At least the boot disk could be removed once it had loaded. Everything was in memory, so there was no need to keep the disk in the drive.

Why does this matter?

The first thing to do was make backups of the diskettes - especially the boot disks. Without a boot disk, the system was useless.

Copying disks was a tedious process - it took about 5 minutes to duplicate a 360K diskette with only one drive, because the disks needed to be swapped multiple times. And my experiences with floppy diskettes was such that I was never satisfied with only one backup copy. I made two backup copies of every important disk (the developer's kit) and three of critical disks (the boot and language disks).

The 3.5" diskettes at the time were about $50 (retail) for a box of 10. Two boxes for backups was another $100.

I realized quickly that having one drive was going to be too much of a bottleneck. (I should have remembered that from my early Apple 2 experience.) There went another $200 for a second drive.

### Getting started

Compiling a program using the development kit was tedious. The C compiler was a 3-pass compiler, assembler and a linker, which meant five separate programs were run for any single-file program.

Having a program divided into smaller modules was worse. The three compiler steps and assembler had to be run for each module, and then the compiled modules had to be linked together to make the program file.

<div style="float:right; margin:5px; padding:3px; border:2px solid black;" >
Batch file for compiling a C module<hr style="margin-top:3px; margin-bottom:3px;">
<pre style="padding:3px; margin-bottom:2px;">
cp68 %1.c %1.i
c068 %1.i %1.1 %1.2 %1.3 -f
rm %1.i
c168 %1.1 %1.2 %1.s
rm %1.1
rm %1.2
as68 -l -u %1.s
rm %1.s
wait.prg
</pre>
</div>

There was no shell. No command-line facility at all. There was a program (`batch.ttp`) that would run a sequence of other programs. Those batch files were _only_ a list of programs to run. There was no iteration, conditionals, or other "script-like" facilities. It was so limited that it was unable to run a separate instance of itself. (A `.bat` file could not run `batch.ttp`.)

There was no "make" facility. If a program consisted of 5 separate source files, the batch file to build it would run the full compile set for all 5 before linking them together.
<div style="clear: right;"></div>

With all the files on diskettes, there was a lot of head movement between the compiler steps, reading the source and header files, and writing the intermediate files. Once past the "toy program" phase, compiling a source file required at least 5 minutes. If a program was divided into multiple separate source modules, a full build could take more than an hour.

After less than a month, I gave up. There was no way I was going to be able to create anything of substance in this environment. 

Don't misunderstand, I was still quite happy with the ST. The supplied word processor (ST Writer) and terminal program (VT-52 Emulator) allowed me to get a lot of personal value from my purchase. And, I knew that once the TOS ROMs were shipped, I would have a couple of different ways that performance would be improved.

### Everything changes for the better in 1986

By July 1986, one year later, many things had changed for the better.

- On the personal side, I was discharged from the Air Force in December 1985. My first civilian programming job paid almost twice the pay that I was getting in the military. (I went from about $14,000 per year to $26,500.) That greatly eased my budgetary constraints. 

- The Atari TOS ROMs were available and I had them installed. (That immediately doubled the amount of usable memory in the system.)

- The new TOS ROMs supported the new double-sided diskettes, increasing disk capacity from 360K to 720K. And, they were effectively faster in that twice as much data could be read or written before needing to move the heads. So I replaced one of my drives with a double-sided drive.

- It was discovered that an Atari 520 ST could be upgraded from 512K to 1 MB by piggy-backing a second set of memory chips to the top of the existing set.

<figure style="float:left; margin:5px; padding:3px; border:2px solid black;" >
<figcaption>Piggyback memory upgrade - 2 of 16 chips</figcaption>
<img src="/images/tech_9/Chip closeup.jpg" width="446" height="378">
</figure>

The new chips would be soldered directly on top of the existing chips. 

They would use the same electrical signals as the chips below them, except for the two address lines that had to be connected to the memory controller.

This seemed a bit "iffy" to me at first, but once I talked to two other people who had it done - and were quite happy with the results - I went ahead with it.

One of the real advantages of the memory upgrade is that it allowed me to create a 360KB RAM Disk. The compilers, assembler, linker and system libraries would all be able to fit on it - greatly increasing the speed.
<div style="clear: left;"></div>

Everything else that was needed fit on one diskette. A complete compilation process could be done without switching diskettes.

What used to be a 25-30 minute process would frequently be done in a couple of minutes.

But the irony here is that other software was being published that eliminated my needs for doing much C-language programming for myself.

- Commercial quality software was becoming available. VIP Professional was the first really serious spreadsheet, and dBMAN was an excellent database system. 1st Word was bundled with early STs, making it the default word processor in many areas. Finally, Flash, a fully-scriptable terminal program was released.

I could now do the real things I wanted to do without having to write a lot of custom code for it.

This configuration served me well for about a year and a half. The material I was producing was either being sent electronically or printed on my dot-matrix printer.

### A massive upgrade in 1988

In late '87 / early '88, I bought an Atari Mega ST 4, which was the 4 MB version of the ST. I also bought the Atari SLM804 Laser printer, the desktop publishing program Calamus, and a 100MB hard drive. There were also some greatly improved development tools available, include a couple of command-line shells and utilities that brought Unix-style scripting to the ST.

<figure style="float:right; margin:5px; padding:3px; border:2px solid black;" >
<img src="/images/tech_9/SLM804-sm.jpg" width="348" height="226">
</figure>

The SLM804 was unique - it didn't have any processor or memory in the printer. Everything was driven and controlled by the Mega ST. The software would allocate a full megabyte of memory to build the page image as a bit map in the computer's memory. The computer would then run a very tight loop to send data at the rate the printer could accept. (It was otherwise unusable while pages were being printed.)

This design greatly reduced the printer's cost, being sold "bare" (no software) for about $1500. A bundle including some fonts and other software sold for less than $2000. Meanwhile, the HP LaserJet II cost about $2500.
<div style="clear: right;"></div>

From my perspective, this gave me a near-professional setup. 

At one point in time, I had a script set up to help me run a Fantasy Baseball League:
* Dial in to an online service (Computer Sports World) to retrieve data (Flash).
* Process the data, generating report segments as text files (dBMAN).
* Create a "text page" version of the report (shell script). 
* Sending the text report through email (Flash).
* Create a newsletter style layout of the output, suitable for printing (Calamus).

I would run that, add whatever commentary information desired, and print the 10 - 15 copies on the laser printer.


### STacy

<figure style="float:left; margin:5px; padding:3px; border:2px solid black;" >
<figcaption>Atari STacy 4</figcaption>
<img src="/images/tech_9/stacy-right.jpg" width="419" height="342">
</figure>

Early in 1990, I bought a STacy 4. It was an Atari laptop with 4 GB RAM and an internal 40 GB hard drive. The original system was intended to be run on 12(!) standard C-cell batteries, but that only provided about 15 minutes of use.

A friend of mine helped develop a ni-cad battery pack that was built into a laptop case. The base of the case was a complete layer of batteries, giving somewhere between 6 and 8 hours of operating time.

The case, batteries, and other electronics brought the total weight to about 25 pounds.In practice, this made it more of a "transportable" computer than a real laptop, but it was still more convenient to take on the road than a full ST system.
<div style="clear: left;"></div>


### Sunset

That was an incredible 3 years for me, but the writing was on the wall. The rapid growth and development in the IBM compatible world, especially with the release of Windows 3.0 for the 80386, made me realize there wasn't a long-term future in the platform.

<figure style="float:left; margin:5px; padding:3px; border:2px solid black;" >
<figcaption>A much younger me</figcaption>
<img src="/images/tech_9/Young ken-sm.jpg" width="280" height="455">
</figure>
It wasn't just CPU speed or memory capacity causing me to become dissatified. 

Video resolutions were better - standard VGA at 640x480 at 16 colors was visually superior to anything the ST could do. But by 1991, the high-end standard was XGA (1024x768 at 256 colors), combined with 14" to 19" monitors. (These were configurations I was using in my day job, and the ST just didn't stand up to the comparison.)

Software selection was definitely better. Productivity tools for the ST just didn't feel as "polished" or as powerful as their DOS/Windows counterparts, and since I worked in a Windows-based environment, file and data compatibility became an issue as well.

I was also missing the ability to expand the system using plug-in cards in slots. Networking multiple computers to share resources became more important.

So in 1991, I put together a 33 MHz 80486 system with 16 MB RAM and another 100 MB hard drive. This pushed the ST into a secondary / supporting role. (I was still using it for publishing, but all the real work was being done on the '486.)
<div style="clear: left;"></div>

Somewhere mid to late 1992, I sold off whatever Atari ST gear I had left at whatever price I could get for it.

The hardest part for me was separating myself from the Atari community in the Washington DC area. This was my first significant involvement with a user group. After six years, the friendships and associations were important to me.

<figure style="float: left; margin:5px; padding:3px; border:2px solid black;" >
<figcaption>A friend of mine from NOVATARI and I decided to show our love of the platform.</figcaption>
<img src="/images/tech_9/Cars-sm.jpg" width="704" height="281">
<figcaption style="text-align: right;">The red Dodge Daytona on the right was mine.</figcaption>
</figure>
