---
layout: post
title: A technical career retrospective part 5a
subtitle: 'Apple ][+ : July 1981 - 1985'
tags: Personal
---

### Why this post?

My dive into the world of personal computing ended up changing the direction of my career. The Apple ][+ was the first step along that path. My interests moved away from the mainframes at this point, never to seriously return. Yes, I would continue to learn and work in a mainframe environment, but my personal efforts were dedicated to personal computing from this point in time.

#### TL;DR

A computer on my desk! Ooh, shiny! (Actually, more of a dull tan color)

#### What to buy?

After doing my research, I decided that the Apple 2 would be the best fit. It seemed to be more oriented toward programming, with a wider variety of tools and utilities. I was also attracted to having the slots on the motherboard, where I could add cards for specific purposes.

### About the Apple 2

The original Apple 2 could be bought with as little as 4K of memory, but by mid-1981 the expectation was that it would be maxed out with 48K.

The Apple 2, along with the Atarir 400/800 and the Commodore PET, used the 6502 CPU. This was a very inexpensive processor compared to the alternatives at the time. It was also more primitive in many ways - but that was beneficial in that you could build systems using the 6502 for less cost than the other processors.

The more sophisticated CPUs used a separate address space for I/O devices, known as I/O Ports. The 6502 instead relied upon "memory-mapped I/O", which means that your I/O devices were accessed by the CPU at regular memory locations.

As a result of this, while the 6502 could address 64K of memory, it couldn't actually use 64K. The system needed to reserve memory space for external devices.

#### Documentation

I strongly believe the Apple 2 was the best documented personal computer of that era - and possibly ever. The `Apple II Reference Manual` provided detailed information about both hardware and software.

The hardware sections included schematics and timing diagrams in sufficient detal to allow people to design add-on devices or new cards to be used in a slot. The software sections provided information on Integer BASIC, System Monitor, Mini Assembler, the 6502 instruction set, "Sweet 16" Emulator, a very detailed memory map, and source code listings for the autostart rom and system monitor; all packed into a very dense 210-page book.

The Apple 2+ also came with a 168-page AppleSoft BASIC manual. This not only provided documentation for the language, but also provided a significant amount of information about the implementation of it. This included identifying

#### Memory organization

A CPU with a 16-bit address space has 65536 addresses that it can reference. In hexadecimal, this is `$0000` - `$ffff`. (The typical documentation at the time uses the dollar sign, `$`, to indicate hexadecimal numbers.)

Each digit represents 4 bits (a "nibble"). Each pair of digits represents 8 bits (1 byte).

The 6502 refers to a memory range of 256 bytes as a page. The page is generally identified by the high-order byte. For example, the addresses `$0000` - `$00ff` is page 0. Page 1 is `$0100` - `$01ff`, and so on.

##### Special pages in the 6502

A couple of pages are considered special by the 6502 processor. Accessing data on page 0 is generally faster than for any other pages. The 6502 has a special addressing mode for page 0, and instructions using that mode are 1-byte shorter and require 1 less clock cycle execute.

The instructions are 1-byte shorter because the target address is only 8-bits intead of 16-bits. The LDA instruction (load register A) for address $009a would be `a5 9a`, where the same instruction for address $129a would be `ad 9a 12`. (Notice that the low order byte is stored first.)

Most instructions using the direct mode of addressing require 4 clock cycles, while the page 0 instructions only need 3.

Since these instructions are shorter and faster than non-page 0 accesses, page 0 addresses are at a premium. The Apple 2 tends to use most of page 0, but there are some bytes available and depending upon what you're doing, you may be able to safely reuse locations generally reserved for the Apple 2 system.

Page 1 is the stack. There are 8 instructions that work directly with the stack. The stack pointer (register `S`) is an 8-bit register, with as assumed high-order address of `$01`. Since the stack is only 256 bytes, that puts a hard upper-limit on how much data can be stored there. A subroutine saving all the user-oriented registers when called uses 6 bytes, which limits the system to a call-depth of 42 functions. (In practice, I've not see code use anywhere near that much.)

These two pages have the same significance, regardless of which 6502-based system you're talking about.

##### Special pages in the Apple 2

Page 2 (`$200` - `$2ff`) is the keyboard input buffer. Text being entered at the prompt are stored here before being processed. This means that the maximum line length was 256 characters.

Pages 4 - 7 (`$400` - `$7ff`) and pages 8 - 12 (`$800` - `$cff`) are the two text and low-res graphics modes screens. Pages `$20` - `$3f` and `$40` - `$5f` were the high-res graphic modes screens. There are 4 pages in 1K, which means that the text and low-res mode screens were 1K, while the high-res modes used 8K.

Pages `$c0` through `$cf` were reserved for I/O, which included built-in devices like the keyboard and joystick adapter, and including usage by cards in the slots.

Pages `$d0` through `$ff` were used by the ROMs in the system.

#### Apple 2 flavors

The original Apple 2 came with an "Integer BASIC" built-in. As the name implies, the only numeric type available were 16-bit integers, giving a usable range of -32768 to 32767. It only supported the text and low-res graphics modes. It was fast, but of limited value for anything beyond games.

There was a more complete BASIC available, called AppleSoft BASIC. Along with supporting floating point numbers and a variety of mathematical functions, it also directly supported the hi-res graphics modes and supported a much larger set of commands.

Originally, AppleSoft was distributed on cassette tape or floppy disk, using more than 8K of system memory. This made it unusable at all on the original 4K and 8K Apple 2, and marginally useful on the 12K version.

When the Apple 2+ was release, it had AppleSoft installed in the ROMs.

Apple also sold a card into which ROMs could be installed. Inserting an Integer ROM card in slot 0 of an Apple 2+, or an AppleSoft ROM card in an Apple 2, provided the ability to switch between the two versions. Later, Apple sold a RAM card (The "Apple Language Card") for slot 0 which allowed either version to be loaded on to it - along with other possible options.

Neither version of BASIC had a "renumber" command built-in. To renumber your BASIC program it was necessary to run an external utility to do it for you.

#### Memory-mapped I/O

A slot had ranges of addresses assigned to it, based on the slot number. So a card in slot 5 would use different addresses than a card in slot 6. This means that any software on a card had to figure out which slot it was in, and adjust any internal address references.

Slot 0 was special, and could only be used by cards designed for it. Most cards had a conventional slot assigned, but in many cases those conventions were not requirements. The disk controller went into slot 6. A printer card in slot 1. Serial cards were used in slot 2. It's not so much that those cards had to be in particular slots as much as most software using them expected to find those cards in those slots.

Slot 7 was also different in that there were some video signals available only to it, but you could still use it for regular cards.

As an example of exactly what this means, reading address `$c000` returns the value of the last key pressed. The high bit is on until address `$c010` is read. (The key value remains in `$c000` until another key is pressed.)

Reading address `$c030` triggers the speaker. Creating a tone required very careful programming to trigger the speaker at a specific frequency. Two side effects of this were that you couldn't create music in BASIC, you needed an assembler routine for it, and you couldn't really do anything else while a tone was being generated.

#### Graphics

The basic screen layout was 24 lines of 40 characters of ASCII text. Switching to low-res graphic changed it to 48 lines of 40 colored blocks. It was the same memory space, but instead of each byte being one character it represented two blocks, top and bottom. Each block was identified by a nibble (4 bits), giving 16 possible colors.

There was also a high-res graphics mode providing a 280 x 192 pixel display.

The precise memory mapping of "screen coordinate to memory address" is complex. There is no simple formula to transpose any given location on the screen to a memory address without the aid of some type of reference table. I'm not even going to try and describe it here - it's well-documented in many other places. But as a result, there were a lot of different algorithms and tables created to optimize graphics management.


