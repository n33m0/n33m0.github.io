---
layout: post
title: "Expanding of variables"
category: CTF, Nebula
---

>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 09](http://exploit-exercises.com/nebula/level09) Agenda**: "There’s a C setuid wrapper for some vulnerable PHP code…"

<center>
	<img src="/images/2015-04-10-expanding_of_variables/tail.jpg">
</center>
<br />
In actual fact, when PHP string is specified in double quotes, [variables](http://php.net/manual/en/language.variables.php) are [parsed](http://php.net/manual/en/language.types.string.php#language.types.string.parsing) within it. Furthermore, through the [complex](http://php.net/manual/en/language.types.string.php#language.types.string.parsing.complex) (allows usage of complex expressions) variable expansion, could be expanded even function call. 

The available script's options shows that we can pass two arguments([$argv](http://php.net/manual/en/reserved.variables.argv.php)) into "_markup_" function. "_$filename_" is used to specify file argument of [file_get_contents](http://php.net/manual/en/function.file-get-contents.php) function. The second one, "_$use_me_"(the name says it all) is never used, so let's fix it through passing to the [print](http://php.net/manual/en/function.print.php). 

{% highlight sh linenos %}
level09@nebula:~$ cat > /tmp/mail
[email {$use_me(sh)}]
level09@nebula:~$ /home/flag09/flag09 /tmp/mail system
PHP Notice:  Use of undefined constant sh - assumed 'sh' in 
/home/flag09/flag09.php(15) : regexp code on line 1
sh-4.2$ getflag
You have successfully executed getflag on a target account
{% endhighlight %}

A few more words to bring some light to the level [source](http://exploit-exercises.com/nebula/level09). We can see that [preg_replace](http://php.net/manual/en/function.preg-replace.php) function is using the [PREG_REPLACE_EVAL](http://php.net/manual/en/reference.pcre.pattern.modifiers.php)("e") pattern modifier which in our case allows the substitution of [backreference](http://php.net/manual/en/regexp.reference.back-references.php) (to the second captured subpattern "_(.*)_") in the replacement string, evaluates it as PHP code ("_spam_" function), and uses the result for replacing the search string. Nevertheless, as mentioned above, the trick by itself happens when the string gets to the output. That's it! 