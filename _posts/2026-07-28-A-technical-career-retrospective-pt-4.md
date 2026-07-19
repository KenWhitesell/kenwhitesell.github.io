---
layout: post
title: A technical career retrospective part 4
subtitle: 'GCOS: January 1980 - sometime in 1984'
tags: Personal
---

### Why this post?

The Honeywell 6000 using the GCOS operating system is the first computer system where I received any substantial training on the system itself. I became quite knowledgeable about the system and how to use it.

#### TL;DR

First exposure to a traditional batch-oriented computer system

#### Disclaimer

In my previous posts, I was able to double-check my memory on some technical details when I wasn't sure of my memory. In the next couple of articles I'll be discussing various aspects of my military career, and I've been unable to find much in the way of specific details to confirm or correct my memory.

Take any details I write here with a large bucket of salt! Some of it I'm more confident than others - but I could simply be more confidently wrong. And for the most part, there aren't any pictures of the environment available either. Images I did find were for more generic installations.

### Training

I'm trying not to make these posts any more autobiographical than absolutely necessary, but my career path at this point was a bit unusual. Most Air Force enlisted personnel go through basic training, then tech school, then are sent to their first duty station. In my case, after completing tech school, they asked me if I wanted to stay and teach - which I did. (I was not alone in this. Their need for instructors was so great that they did this for about 3 or 4 of us that year.)

Honeywell also had a training center there, and their instructors were quite willing to share information with us. I spent a lot of time hanging around them - and they gave me student copies of all their materials to go along with the Air Force coursework. Over a 3-year span (March 1980 - March 1983), I qualified to teach both the regular Computer Programming course and the Communications Programming course. Eventually, I was promoted into the systems shop, to be one of two systems programmers responsible for keeping the system up and running, where I ended up having regular access to the Honeywell system engineers who took care of the hardware for us.

Over the four year period of this post, I was trained on the Honeywell 6000 GCOS system in Fortran, COBOL, GMAP (GCOS assembler), system tools and utilities, JCL, dump analysis, system administration, operations, system configuration and installation, along with Datanet 355 communication processor configuration and programming, and probably a few other topics as well that I'm not thinking of right away. (That's not counting the formal instructor training and ancillary courses associated with that - but they are not relevent here.)

### The computer center

The training center on Keesler was a converted aircraft hanger. There were classrooms and offices on two levels on each side of the building.

The classrooms were air conditioned - usually. The larger hangar itself was also - to a degree - climate controlled, but there's only so much that can be done with a large metal building when it's 95 degrees with 95 percent relative humidity. (The "90/90" days as we used to call them.)

In the middle of the hangar floor was a big open space. Toward one end there was an enclosed computer room on an elevated floor, with air conditioning units next to it. The room was elevated about 4 feet above the hangar floor. There were two or three doors into it. The front door was for the students to come up, drop off their card decks, and pick up their output. The other doors were for access to the areas where the staff worked, including a door to bring up the boxes of paper and other consumables.

Just outside the student entrance, there was a large open area called the "pit" with long tables and chairs.
Students could spread out their output and really look at the program to work on it.

One common event in a Biloxi summer was the afternoon thunderstorms. You could expect some type of weather event every day around 2 PM. We used to walk out to the hangar entrance to watch the "light show" when we started hearing the thunder.


### Quite a change

I had used punch cards for some of my work on MTS, but most of my time with the HP and MTS were with using terminals.

We were pretty much forced to use the punch cards during the first half of tech school. I think the terminal room at the time only had six terminals, and they were effectively reserved for the students in other classes. We were eventually shown how to use the terminals, and told we could use them if they were available.

The terminals were Honeywell VIP7700 terminals. They were of a "page mode" type, similar to the IBM 3270, so I had no problem getting used to them.

Due to the shortage of terminals relative to the number of students at the time, we were still strongly encouraged to run our programs and send the output to the printer rather than working continuously on the terminal. We would then log off the terminal, get our output from the window, then work on debugging and correcting the code on paper in an area known as the pit.

Of course, while you're trying to read your code and focus on fixing your bugs, you were also listening to a dozen other students rustling paper, muttering and cursing while they're trying to fix their own programs.

### Code libraries

I was first introduced to the concept of a code library in that class. It wasn't even remotely similar to `RCS`, `CVS`, `SVN` or `GIT`. It was just a tape storing file. It was your protection against disk failure or a standard facility for sharing code with a different site.

A code library was a tape containing your source or object code. There was a program that managed the contents of that tape. Jobs would have directives to add, remove, or replace entries on the tape with updated files. The concept was that every time a program was put into production, the source code would be added to the source tape and the object deck added to the object tape.

I seem to remember that the directives needed to be applied in the order that the files appeared on the tape, making it necessary to track the order in which the files were on the tape.

We were also taught that every programming group would have a "code librarian" as a defined role. This individual was responsible for ensuring that all production code was saved to a tape. (I later learned from some people that this wasn't universally true...)

### What was different about the Honeywell 6000?

I learned enough about the IBM / Amdahl architecture to have a general idea of how it functioned, but not enough to actually program it at that level. It was quite a surprise to start working on the H-6000 and learn just how different it was.

The most striking difference is that it used a 36-bit word, instead of a 32-bit word. These words were divided into either 6-bit (BCD) or 9-bit (ASCII) characters. The BCD encoding was the default. All the batch jobs, source code, punch cards, printer output - all used BCD. The ASCII character set was intended to be used only when transmitting data to/from other systems. (e.g. Writing a tape that was going to be used elsewhere.)

As a result of the BCD encoding along with the format of the instruction set, the natural representation of the data was octal (base 8) instead of hex (base 16). Addresses were 18 bits long - a half-word, or 6 octal digits. The op code was another 9 bits - 3 octal digits. Using octal then felt natural and made it very easy to read dumps.

#### The instruction set

The standard instruction set instructions were one word, usually consisting of an address, op code, flags, and register ids. Most instructions performed an operation on a register, possibly with a reference to a memory location. Memory-to-register or register-to-memory operations involved either a word, half-word, or a word-pair.

There were also Extended Instruction Set (EIS) instructions that were 1 - 4(?) words in length. The EIS instructions added features that are still unique in my experience. It provided addressability to an individual bit within a word. It allowed any arbitrary number of bits starting from any bit position in a word to be copied to any bit position in the destination location. That bit string could span words either in the source or the destination.

This allowed the programmer (or compiler) to more easily copy character data without worrying about word alignment, or the location of characters within a word. And, the instruction codes for doing this were the same, regardless of whether it was accessing 6 or 9-bit characters, or 4-bit bcd numeric digits.

#### No virtual memory!

The version of GCOS we were using did **not** support any type of virtual memory. At the time I started, the computer only had 192K words of memory. Without virtual memory, the system could only run as many programs as would fit in real memory. Program memory had to be contiguous as well - there was no concept of "pages" where a program could be segmented into different parts and loaded into different areas of memory.

I seem to remember at the time that once a program was loaded into memory, it couldn't be moved, either. I think there were some control blocks within the program that would get real memory addresses loaded into them, and so were locked into their location for the duration of their execution.

As a result, there would be times when programs were queued up waiting for a contiguous block of memory large enough to run.

#### Limits on memory usage

You'd learn pretty quickly not to rely upon the system defaults for memory requirements. Those defaults were generally adequate for large production-type programs, but were far larger than what was usually needed for student projects.

The COBOL compiler would normally allocate 32K, but I think it could be reduced to around 26K to 28K for a compile. The other languages used, FORTRAN and GMAP allocated less, but still more than what was needed.

The EXECUTE statement for compiled programs would allocate 16K, but most of the student programs could run in 4K to 8K, depending upon which language it was written in.

If you thought you were going to be running (or trying to run) your program multiple times, you'd find the memory usage report within your output, add some small fudge factor, and use _that_ as the memory allocation for your job.

It would help you, because if the system had a 30K memory range available, it would pick the next job waiting to run needing 30K or less, making a job needing 32K to wait. It would also help everyone else, because if you're not using 32K, then you're making more memory available for everyone else to get their jobs through.

(We weren't officially taught this - it just seemed to be knowledge that was passed along from class to class among people working in the pit.)

I don't remember exactly when, but while I was there, the system was upgraded from 192K to 256K. There was also an OS upgrade that allowed for programs to be moved in memory. These two changes greatly improved job throughput, making it far more practical to allow more students to spend more time working on terminals instead of using cards.

#### No remote access!

This wasn't so much a technical difference as it was an environmental one. The computer room, pit, and the student lab areas were only open Monday-Friday 6 AM - 10 PM. Within that time, I was busy enough doing my real job that I wouldn't dare work on a personal project.


### The next step

In March 1983, I left Keesler for my first real duty station in Washington DC. I was stationed there for almost three full years, but only worked on the H-6000 for about the first half of my stay. That became my first real job where I needed to "crank out copious quantities of COBOL code".

The systems I worked on were not interactive in nature. They were typical batch processing of one of two types. The program either read a file and produced reports based on the data, or would read a data file containing information to update in the master file.

I did have one project that was intended to be interactive - and taking advantage of the terminal page mode. What I most remember was being quite surprised to discover that the FORTRAN library for handling the screen was far superior to the COBOL library for doing the same.

If you created a 2 dimensional array of same-sized fields on the screen, the FORTRAN library would return that data directly to a character matrix. Creating the same screen in COBOL appeared to require individual data elements for each field, making it far more awkward to process 50+ fields. (If there _was_ a way to get that data to populate a COBOL table, the two of us working on this were unable to discover it.)

### A side-bar : Recurring themes

I've been around long enough to see some patterns repeat themselves over time. I will occasionally see some software product, technique, or standard identified as "new", when in my mind it's just an update of something I had seen 20 years previously.

I will be pointing out either directly or by analogy, when what I have worked on in the past appears to be quite a similar pattern to something used more recently.

And the first is:

#### Terminal control

Like the IBM 3270, the Honeywell VIP 7700 was a page mode terminal. One of the features of these types of terminals is that they buffer everything locally in the terminal until pressing enter. When the enter key was pressed, the page is sent to the computer for processing.

But there's more to this than that. These terminals can define "fields" of specific position, length, and data type on the screen. Data could only be entered within those fields - the rest of the screen was uneditable.

There were two specific "tab" keys on the keyboard, forward tab and back tab. These would move the cursor to the next or previous field, respectively.

The field types were either alphanumeric or numeric only.

If a program used "screen mode" for interacting with a terminal, the page would be defined with the appropriate field marks to identify those fields. The data fields would also be defined to receive the data submitted by the terminal.

(Does anyone else see the parallel between this and the HTTP protocol?)
