---
layout: post
title: A technical career retrospective part 1
subtitle: 'HP-200F: January - June 1978'
tags: Personal
---

## Why this post?

Someone whose work I respect tremendously, and whose product has truly changed my life, suggested
I write "a technical restrospective on your career, discussing practical programming". As I have no other way to repay my debt of gratitude, I figured at least I could give this a shot.

### TL;DR

Writing programs 50 years ago was a lot different than today.

## Getting started

Between my junior and senior years of high school (summer '77), we moved from the Baltimore area to Ann Arbor Michigan. (Go Blue!) This included being moved to a high school with district-wide access to a minicomputer. They offered two 1-semester classes in Programming in Basic.
<figure style="float:left; margin:5px; padding:3px; border:2px solid black;" >
<figcaption>Digi-Comp I</figcaption>
<a href="https://en.wikipedia.org/wiki/File:Digicomp_I.JPG">
<img resource="https://en.wikipedia.org/wiki/File:Digicomp_I.JPG" src="/images/tech_1/Digicomp_I.JPG" height="160" width="330"></a>
<figcaption>
<div>Pterre, <a href="https://creativecommons.org/licenses/by-sa/3.0/deed.en">CC BY-SA 3.0</a>,<br>via Wikimedia Commons</div>
</figcaption>
</figure>
While I had some previous exposure to programmable calculators ([TI SR-52](http://www.datamath.org/Sci/WEDGE/SR-52.htm)) and educational toys like the [Digi-Comp&nbsp;I](https://en.wikipedia.org/wiki/Digi-Comp_I) and a variety of the Radio Shack electronics kits, this became my first real introduction to programming.

The minicomputer was an HP-2000F, programmable in Basic. Our school's primary interface to that system were [Bell ASR 33 Teletypes](https://en.wikipedia.org/wiki/Teletype_Model_33) with the paper tape punch/reader.
<br clear="all" />
Due to some class scheduling issues and requirements caused by the change in schools, I didn't actually get to start the classes until the second semester (January 1978).
<figure style="float:right; margin:10px; padding:3px; border:2px solid black;" >
<figcaption>Teletype Model 33 ASR teletype</figcaption>
<a href="//en.wikipedia.org/wiki/File:Teletype-IMG_7287.jpg">
<img resource="//en.wikipedia.org/wiki/File:Teletype-IMG_7287.jpg" src="/images/tech_1/Teletype-IMG_7287.jpg" height="465" width="310"></a>
<figcaption>
<div>Rama &amp; Musée Bolo, <a href="https://creativecommons.org/licenses/by-sa/2.0/fr/deed.en">CC BY-SA 2.0 FR</a>,<br>via Wikimedia Commons</div>
</figcaption>
</figure>
### The teletype
I had previously taught myself how to type on an old Olivetti mechanical typewriter, where you
learned that finger pressure on the keys is important. You had to be firm and reasonably consistent.

But those teletypes kicked that principle up two or three notches. The keys were **firm** - you had to press them with authority. There was no real concept of "touch typing" in that situation.

They also worked at 110 baud - call it 10 characters per second, so getting a program listing of any size could be time-consuming.

They were also loud, with a very distinct mechanical noise when they were operating. The computer lab for the school was a portion of a classroom walled-off, with a door. My memory puts the size as somewhere around 12' x 24', with space for 4 teletypes on each side of the room, plus a smaller number (4? 6?) of video terminals, and storage for paper and paper tape rolls.

Put 6-8 students in there either running or listing programs, and normal-voice conversations simply weren't possible.

### Debugging

You learned quickly that once a program got beyond a certain size, it was more effective to get your listing and walk back out into the classroom to hand-write your changes and desk-check them.

There wasn't any sort of interactive debugger available. You ran your program until you either quit it, it ended, or it had an error. Debugging meant walking through your program line-by-line, "playing computer" along the way.

In my case, that meant I'd get some scrap paper and set aside space for individual variables.
I would then walk through the program, and perform the logic in each line. Each time a
variable was changed, I'd cross out the old value and write the new. If necessary, I'd use my
calculator to figure the new value.

After making the changes on paper, I'd go back into the lab and try again. I'd then repeat the
process until I had a working program.

### Limitations of the HP-2000F

One of the many limitations of that system was that it was difficult to share blocks of code
between programs.

If you had a subroutine that you wanted to move from one program to another,
you effectively had two choices.

- List that code to the paper tape punch to get a
physical copy and then load your new program and add the code from the tape.
Or,
- Copy the orignal program to a new name, delete all the code other than the code you want to
move, renumber that code to a higher range, and save it. Then, open the file containing the
target program and append the file containing the snippet.
(The append command had the
limitation that the code being appended must have line numbers higher than the highest
number in the existing file.)

### Limitations of the Time-Shared Basic

BASIC as a language and as implement in the HP2000 series had a number of interesting constraints. My memory of most of these are rather clear, but I won't swear by them all.

Everything by default was upper-case. (With an appropriate terminal, you could create literal character strings containing lower-case characters, but you had to do extra work to have those show back up as lower case. Otherwise, everything was folded back to upper case.)

Variables were limited to being either a letter or a letter followed by a number. (`X` and `X1` were valid variable names, `XA` is not) They are all global in scope.

You could define up to 26 functions, with names `FNA` to `FNZ`. A function could take one parameter, evaluate one expression, and return one value. The parameter passed as the argument is local to the function, while any other variable references are to the variables in the global scope.

Subroutines did not take parameters. You would allocate a set of variables for the subroutine to use, copy the values to those variables in your main program, call the subroutine (`GOSUB`), and copy any updated variables back to where they need to be.

Matrices are two-dimensional only, and limited to (roughly) 5000 elements - but they all come out of the global memory space. With any decent sized program, you might be lucky to create a matrix with 2000 entries or so.

### Accessibility limitations

The most serious constraint for me was available time to access the system. At best I had perhaps a half-hour before school and an hour after the regular school day to spend in the computer room. As I was becoming increasingly enthralled with writing code, this was becoming a bit of a problem.

I was to the point of writing my code out on paper in the evenings, then waiting at the school door in the mornings to be let in.
<figure style="float:right; margin:10px; padding:3px; border:2px solid black;" >
<figcaption>DECwriter II LA-36</figcaption>
<img src="/images/tech_1/Decwriter_iii.jpg" height="332" width="250">
<figcaption>
<div>This picture is really a DECwriter III,<br>but it's close enough to the II.</div>
</figcaption>
</figure>

Finally, someone told me that the local community college had a lab that was open for use in the evenings. They had about a dozen DECwriter LA30 terminals that could be used for dial-in access - at 300 baud even!

They're the first devices I've ever used where "touch-typing" would have been considered practical. The (relatively) quiet "buzzzz" of the dot-matrix print heads were a far cry from the "ticka-chunka-CLUNK" of the teletypes. And since they put out text at more than 3 times the speed of the teletype, it was a lot more practical to print long listings. Also, since they were 132-column printers, it added more flexibility with the way you formatted output that wasn't available on the 80-column teletypes.

The lab also had a number of video terminals. They were useful when making lots of program changes that I had written on paper, or playing the various computer games. (I didn't care for wasting the paper playing games.)

Side note: Even though the lab really was intended for the community college students, there was never anyone there who even asked. It was never full in the evenings so no one ever needed to wait for access to a terminal. It also appeared to me that at any given time at least half of the people there were high school students.

<figure style="float:left; margin:10px; padding:3px; border:2px solid black;" >
<figcaption>Lear Siegler ADM-3A</figcaption>
<img src="/images/tech_1/ef52_12.jpg" height="225" width="300">
<figcaption>
<div>I don't remember if this was the exact model<br>I rented, but it's close. I do remember<br>it having a rounded profile.</div>
</figcaption>
</figure>

After the school year ended, I got a summer job. One of the first things I did was to rent a video terminal and paid to have a second phone line installed in the house. That ended almost any need to go to the college, except to make the occasional print of a listing or output.

Since I no longer had access to a paper tape punch, I had no way to keep any machine-readable copies of those programs. (Of course, hindsight shows that the paper tape became obsolete soon after, so keeping that would have been of less value than the printed listings.)