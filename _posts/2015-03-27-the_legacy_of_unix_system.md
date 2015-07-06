---
layout: post
title: "The legacy of unix system"
category: CTF, Nebula
---

>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 06](http://exploit-exercises.com/nebula/level06) Agenda**: "The flag06 account credentials came from a legacy unix system."

<center>
	<img src="/images/2015-03-27-the_legacy_of_unix_system/e4bf52bf.jpg">
</center>

<br />

If we'll digging in flag06 home, we'll find nothing interesting. However, if we try to [grab](http://unixhelp.ed.ac.uk/CGI/man-cgi?grep) users data from "[*/etc/passwd*](http://man7.org/linux/man-pages/man5/passwd.5.html)" (a text file that describes user login accounts for the system) it turns out that flag06 encrypted password is not stored in "*/etc/shadow*" (which instead of passwd is readable by [superuser](https://en.wikipedia.org/wiki/Superuser) only):

{% highlight sh linenos %}
level06@nebula:/home/flag06$ cat /etc/passwd | grep "flag06"
flag06:ueqwOCnSGdsuM:993:993::/home/flag06:/bin/sh
{% endhighlight %}

To crack that password hash, let's try to use old school pop-tool - "*[JTR](http://en.wikipedia.org/wiki/John_the_Ripper)*". But firstly need to install it as nebula account (pw the same). 

{% highlight sh linenos %}
level06@nebula:/home/flag06$ su nebula
Password: 
nebula@nebula:/home/flag06$ sudo apt-get install john
[sudo] password for nebula: 
Reading package lists... Done
.................................................
Setting up john (1.7.8-1) ...
nebula@nebula:/home/flag06$ exit
exit

level06@nebula:/home/flag06$ john /etc/passwd
Created directory: /home/level06/.john
Loaded 1 password hash (Traditional DES [128/128 BS SSE2])
hello            (flag06)
guesses: 1  time: 0:00:00:00 100% (2)  c/s: 25100  trying: 12345 - biteme
Use the "--show" option to display all of the cracked passwords reliably
{% endhighlight %}

Success! At line 14 of the above codeblock, we can see that the flag06 account's password is - "*hello*" phrase.