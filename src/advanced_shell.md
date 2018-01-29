Advanced shell usage
====================

At this point I'm assuming that you already know how to use the shell to a
basic level. We're going to talk about a few tips and tricks to get better at
it.

Tab completion
--------------

One of the things that seems tedious about using the shell at first is needing
to type out so many long commands. The shell actually provides many ways to
make this quicker. The first thing we'll look at is tab completion. This uses
the "tab" key on your keyboard. Suppose I open my terminal and I want to `cd`
into a directory called `current/emat10006web/`. It's annoying to have to type
out the full folder name and there's also a good chance I'll make a mistake
when typing but there's an easier way. Type `cd cu` and then push tab. If
there's only one possible file or directory name beginning with `cu` then it
will complete to `cd current` i.e.

~~~~
$ cd cu <tab>
$ cd current/
~~~~

Note that I haven't pushed the enter key yet as I'm still typing this command
so I'm not ready to run it yet. Now if `emat10006web` is the only file or
directory in `current` I can simply push tab again to get:

~~~~
$ cd current/ <tab>
$ cd current/emat10006web
~~~~

As it happens there are several files in `current` and they don't all start
wit the same letter so instead I see

~~~~
$ cd current/ <tab><tab>
emat10006/    emat10006web/ primevenv/    tmp/
~~~~

Note that the first time I push the tab key nothing happens. This is because
it's ambiguous what filename should be used to complete the command. The
second time I push tab the shell shows me a list of possible completions. I
want one of the ones that begins with "e" so I'll type that and push tab again

~~~~
$ cd current/e <tab>
$ cd current/emat10006
~~~~

Now because there were two possibilities beginning with "e" and each begins
with "emat10006" it has completed that much for me. To get the full
"emat10006web" I'll need to add a "w" and push tab again

~~~~
$ cd current/emat10006w <tab>
$ cd current/emat10006web/
~~~~

At this point I've completed the command and can push enter to run it. The
description above may sound overly complicated but if you get used to using
tab completion it will start to seem natural: you just have to try it.

Rerunning commands
------------------

Something else that helps is being able to locate previously run commands with
the arrow keys. After running some commands in the shell see what happens when
you push the up and down arrow keys. The shell allows you to navigate through
all of the previously typed commands. You can find a previous command and
either run it as it is or edit the command and run it. This is particularly
useful if you make a mistake while typing a long command: just push "up" and
then fix the mistake and push enter again. You can see a list of all
previously typed commands by running the `history` command e.g.:

~~~~
$ history
...
 1986  mv sum.c array.c
 1987  make
 1988  vim Makefile
 1989  touch array.c
 1990  make
 1991  ./array.exe
 1992  touch array.c
 1993  cat $!
 1994  touch array.c
 1995  cat array.c
 1996  make
 1997  ./array.exe
 1998  PS1='$ '
 1999  cd current/
 2000  cd ..
 2001  history
~~~~

To see only those commands containing the string "gcc" you can filter the
output using `grep` like so

~~~~
$ history | grep gcc
 1707  gcc -Wall -std=c99 argcount.c -o argcount.exe
 1713  gcc -Wall -std=c99 getinput.c -o getinput.exe
 1716  gcc -Wall -std=c99 getinput.c -o getinput.exe
 1723  gcc -Wall -std=c99 getinput.c -o getinput.exe
 1727  gcc -Wall -std=c99 getinput.c -o getinput.exe
 1731  gcc -std=c99 -Wall outerr.c -o outerr.exe
 1737  gcc -std=c99 -Wall retcode.c -o retcode.exe
 1817  gcc conditional.c
 1819  gcc conditional.c
 2002  history | grep gcc
~~~~

The numbers next to each command in the history output can be used to repeat
any command. To repeat command 1817 simply type `!1817` and hit enter:

~~~~
$ !1817
gcc conditional.c
gcc: error: conditional.c: No such file or directory
gcc: fatal error: no input files
compilation terminated.
~~~~

The previously typed command can always be referred to as `!!`. This is useful
if you want to rerun that command but with an extra string before or after it.
For example on Linux or OSX many administrative tasks need to be run with
`sudo`. If you forget to put `sudo` at the beginning of a command you can then
repeat it with `sudo` by typing `sudo !!`:

~~~~
$ apt-get install gcc
E: Could not open lock file /var/lib/dpkg/lock - open (13: Permission denied)
E: Unable to lock the administration directory (/var/lib/dpkg/), are you root?
$ sudo !!
sudo apt-get install gcc
[sudo] password for oscar:
Reading package lists... Done
...
~~~~

You can also refer to the last word of the previously typed command as `!$`.
This is useful when you want to run several commands on e.g. the same
filename. A common example is to create a directory and then `cd` into it:

~~~~
$ mkdir some_long_directory_name
$ cd !$
cd some_long_directory_name
~~~~

There is also a way to search for a command and run it using
Ctrl-R. Push the control key and then the R key. Now type some part of a
previously typed command and it will suggest a command line to run. If the
command line is correct you can simply push enter to run it. Try it!

Another good way to reduce typing out long commands is to save those commands
into files. This is covered in the next section about Makefiles.

------------
Next section: [makefiles](makefiles.html)
