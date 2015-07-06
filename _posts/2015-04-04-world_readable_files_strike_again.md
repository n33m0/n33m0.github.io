---
layout: post
title: "World readable files strike again"
category: CTF, Nebula
---

>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 08](http://exploit-exercises.com/nebula/level08) Agenda**: "Check what that user was up to, and use it to log into flag08 account."

<center>
	<img src="/images/2015-04-04-world_readable_files_strike_again/strikes-again.jpg">
</center>
<br />
All right!!! So basically, what do we have to start with, is a single [pcap](http://en.wikipedia.org/wiki/Pcap) file format:
{% highlight sh linenos %}
-rw-r--r-- 1 root   root    8302 2011-11-20 21:22 capture.pcap
{% endhighlight %}
If working with TCP based protocols it can be very helpful to see the data from a TCP stream in the way that the application layer [sees it](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).


Without doubts [Wireshark](http://en.wikipedia.org/wiki/Wireshark) is a wounderful tool (nonetheless, we aren't able to use [X11-server](https://en.wikipedia.org/wiki/X_Window_System) on Nebula VM. Actually, all is [possible =)](http://www.quora.com/How-valid-are-the-ideas-in-Athenes-theory-of-everything) but that's definitely is not the best way). It worth to notice that there's also exist console version called [tshark](https://www.wireshark.org/docs/man-pages/tshark.html) but unfortunately, attempt to setup it on VM due to some failures was unsuccessful. That's why I had to use what was at hand, in particular [tcpflow](http://www.circlemud.org/jelson/software/tcpflow/):
{% highlight sh linenos %}
level08@nebula:/home/flag08$ tcpflow -cer ./capture.pcap
{% endhighlight %}
<center>
	<img src="/images/2015-04-04-world_readable_files_strike_again/screen_0.png">
</center>
Blue color indicates client to server flow and the red one is server to client. The default behavior in case of printing to file/console is converting all non-printable characters to the "." character. We have four dots in the password, let’s not guessing but making clear for sure what it exactly is by using wireshark (cannot escape from fate) on the outer host. 

Firstly need to pass this instance file out and [netcat](http://en.wikipedia.org/wiki/Netcat) will help in this.
{% highlight sh linenos %}
level08@nebula:/home/flag08$ nc -l 8888 < ./capture.pcap
# And then on the outer host:
nobody@home$ wget -O capture.pcap 192.168.56.102:8888
{% endhighlight %}
Secondly, check out on the wireshark:
<center>
	<img src="/images/2015-04-04-world_readable_files_strike_again/screen_1.png">
</center>
If grab the [ASCII](http://www.asciitable.com/) table it appears that "_7F_" hex represents [delete](http://en.wikipedia.org/wiki/Delete_character) and "_0D_" - [carriage return](http://en.wikipedia.org/wiki/Carriage_return) symbols respectively. The final note as conclusion, after applying "_DEL_", the passphrase (flag08 account) becomes - "_backd00Rmate_". Hell yeah (òÓ,)_\,,/ !