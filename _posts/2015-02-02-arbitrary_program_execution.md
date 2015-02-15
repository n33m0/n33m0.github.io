---
layout: post
title: "Arbitrary execution"
category: CTF, Nebula
---




>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 02](http://exploit-exercises.com/nebula/level02) Agenda**: "There is a vulnerability in the program that allows arbitrary programs to be executed, can you find it?"

<center>
	<img src="/images/2015-02-02-arbitrary_program_execution/Cf2oDY4yeL4.jpg">
</center>

<br />

Hovewer, what we have notice after code review at this stage?
The essential thing is usage of "*char *getenv(const char *name)*" [function](http://man7.org/linux/man-pages/man3/getenv.3.html), which get an environment variable (*...obviously, thanks cap!*), and returns a pointer to the corresponding value string.
And the next major usage - "*int [system](http://linux.die.net/man/3/system)(const char *command)*". It's executes a command specified in "**command*"  by calling "*/bin/sh -c*" command (*command-command-command =)*), and returns after the command has been completed.

Well, is there some clue? Definitely, the point is on replacing $USER variable value with desirable tricky command (again =)).

{% highlight sh linenos %}
level02@nebula:~$ export USER=";getflag;"
#OR
level02@nebula:~$ export USER="&getflag;"
#OR
level02@nebula:~$ export USER="|getflag;"
#OR
level02@nebula:~$ export USER="&& getflag;"
{% endhighlight %}

 * [export](http://linuxcommand.org/lc3_man_pages/exporth.html) - set export attribute for shell variables.
 * ";"  - some kind of command separator.
 * "& ([ampersand](http://hacktux.com/bash/ampersand))" -  is a builtin control operator used to fork processes. From the Bash man page, "If a command is terminated by the control operator &, the shell executes the command in the background in a subshell". 
 * "&&" -  Logical AND, lets you do something based on whether the previous command completed successfully 
 * "\|" - bitwise OR, will allow the successive command to execute if the preceding fails. 
 * quotes is used to escape special chars in env var value.

That's it! The final point, execute "*/home/flag02/flag02*" to check out the result.