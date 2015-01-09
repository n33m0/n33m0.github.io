---
layout: post
title: "Suspicious directories"
category: CTF, Nebula
---




>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 00](http://exploit-exercises.com/nebula/level00) Agenda**: "This level requires you to find a Set User ID program that will run as the "flag00" account.
> You could also find this by carefully looking in top level directories in/for suspicious looking directories."

<center>
	<img src="/images/2015-01-09-suspicious-directories/dog.jpg">
</center>

To omit all “*permission denied*” messages from “[find](http://unixhelp.ed.ac.uk/CGI/man-cgi?find)” we can redirect the Standard Error Output from generally Display/Screen to some file (e.g. to a special file [/dev/null](http://stackoverflow.com/questions/762348/how-can-i-exclude-all-permission-denied-messages-from-find)) and avoid seeing the error messages on the screen! For more details, you can get acquainted with [man](https://en.wikipedia.org/wiki/Man_page) util.


{% highlight sh linenos %}
	$find / -user flag00 2>/dev/null

	/bin/.../flag00
	/home/flag00
	/home/flag00/.bash_logout
	/home/flag00/.bashrc
	/home/flag00/.profile
	/rofs/bin/.../flag00
	/rofs/home/flag00
	/rofs/home/flag00/.bash_logout
	/rofs/home/flag00/.bashrc
	/rofs/home/flag00/.profile
{% endhighlight %}


It's easy to notice those "suspicious looking directories"). BTW as we can find out that "*rofs*" (if you're intrigued and noticed this of course) is a read-only filesystem that allows to create a read-only mountpoint of a read-write directory on the system (or at least, something similar =)). However, it doesn't matter.

{% highlight sh linenos %}
$ls -l /bin/.../flag00
	-rwsr-x--- 1 flag00 level00 7358 2011-11-20 21:22 /bin/.../flag00
{% endhighlight %}

At last, the execution of "*/bin/.../flag00*", provide to such desirable hint. 
Therefore, the execution of "*getflag*" file is our general purpouse at each level. 

PS. some useful links:

 * [wikipedia.org/wiki/Setuid](http://en.wikipedia.org/wiki/Setuid)
 * [wikipedia.org/wiki/File_system_permissions](http://en.wikipedia.org/wiki/File_system_permissions)

 If you're looking for something fun-filled and aimed at absolute beginners, you can try [wargame](http://overthewire.org/wargames/bandit/) offered by [OverTheWire](http://overthewire.org/wargames/) community.
