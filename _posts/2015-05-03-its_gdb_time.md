---
layout: post
title: "It's GDB time!"
category: CTF, Nebula
---

>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 13](http://exploit-exercises.com/nebula/level13) Agenda**: "There is a security check that prevents the program from continuing execution if the user invoking it, and it does not match a specific user id"

<center>
	<img src="/images/2015-05-03-its_gdb_time/f0fd70bd24c9be9f87566b2142c0.jpg">
</center>
<br />
The only applicable from my perspective thing to that issue was [GDB](http://linux.die.net/man/1/gdb) ("_gdb /home/flag13/flag13_"). After [disassembling](http://visualgdb.com/gdbreference/commands/disassemble) main function ("_disas main_") I cut out some chunk (whole list are to huge) which is in charge of id comparison ("_(getuid() != FAKEUID)_" - "_cmp eax,0x3e8_") and further [flow control](https://en.wikibooks.org/wiki/X86_Assembly/Control_Flow):

<img src="/images/2015-05-03-its_gdb_time/disas_0.png">

So the next line after comparing, makes jump to "_<main+109>_" if value of [$eax](http://www.swansontec.com/sregisters.html) register is equal to "_0x3e8_", otherwise execution flow will ends with calling "_exit_" function ("_0x0804852c <+104>_"). Therefore, we plan to act as follows:

- Make [breakpoint](https://en.wikipedia.org/wiki/Breakpoint) at comparing entry ("_0x080484f4_" [virtual](http://en.wikipedia.org/wiki/Virtual_address_space) address)
- Change $eax value to "_0x3e8_".
- Obtain token.

Let's do this!

<img src="/images/2015-05-03-its_gdb_time/disas_1.png">

Resources:

 * [Prettify my gdb](http://stackoverflow.com/questions/209534/prettify-my-gdb)
 * [What does @plt mean here?](http://stackoverflow.com/questions/5469274/what-does-plt-mean-here)
 * [Introductory Intel x86: Architecture, Assembly, Applications, & Alliteration](http://www.opensecuritytraining.info/IntroX86.html)