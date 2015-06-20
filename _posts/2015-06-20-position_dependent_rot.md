---
layout: post
title: "Position dependent rotation"
category: CTF, Nebula
---

>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 14](http://exploit-exercises.com/nebula/level14) Agenda**: "It encrypts input and writes it to standard output. An encrypted token file is also in that home directory, decrypt it :)"

<center>
	<img src="/images/2015-06-20-position_dependent_rot/c2dd6bd93fcd7be403b850dbf.jpg">
</center>
<br />
During the investigation under the hood, it's was easy to recognize position dependent [ROT](https://en.wikipedia.org/wiki/ROT13) (means, not 13 =). In current case, each char's shift is directly proportional to its position in the sequence):
{% highlight sh linenos %}
level14@nebula:/home/flag14$ ./flag14 -e
111111111
123456789
level14@nebula:/home/flag14$ ./flag14 -e
aaaaaaaaa
abcdefghi
{% endhighlight %}
Thus it was easy to write appropriate decoder:
{% highlight python linenos %}
#!/usr/bin/python
import sys

def decode(string):
    if string is not None:
        result = ""
        for i in range(0, len(string)):
            result += chr(ord(string[i]) - i)
        return result


def readContentOf(filename):
    try:
        with open (filename, "r") as tokenFile:
            return tokenFile.read().replace('\n', '')
    except IOError:
        sys.stderr.write('Problem reading:' + filename)


def main():
    if (len(sys.argv) >= 2) :
        text = readContentOf(sys.argv[1])
        print "Your token is: ", decode(text)
    else :
        print "Please specify the toke_file"


if __name__ == "__main__":
     main()
{% endhighlight %}
And obtain the token:
{% highlight sh linenos %}
level14@nebula:~$ ./decoder.py /home/flag14/token 
Your token is:  8457c118-887c-4e40-a5a6-33a25353165
{% endhighlight %}

Resources:

 * [https://developers.google.com/edu/python/](https://developers.google.com/edu/python/)