Make files
==========

Why make files?
---------------

You'll probably find that you're repeatedly typing out long commands such as
`gcc -std=c99 -Wall stuff.c -o stuff.exe`. In the last section we looked at
ways to speed this up by reusing previously typed commands but this technique
only gets us so far. What if there's a very large number of commands to type
in order to compile our program? The Python interpreter is a C program and
compiling it requires compiling some 500 or so .c files. We don't want to have
to type out the commands for each of those!

The standard solution to this is to use make files. There are several
advantages to using make files:

- Saves typing out the commands
- Don't need to remember the commands
- Don't need to ever learn the commands (e.g. when compiling someone else's
  code).
- Can selectively re-run commands which is more efficient for compiling large
  projects.

A standard convention for distributing the code of C programs is that to
compile a C program you first download the source code. Let's say that the
source code is in a file called `coolproject-3.1.zip`. You expect to unzip
that file and find a folder called `coolproject-3.1` which you will `cd`
into. Now in that folder you will run two commands to build the project

~~~~
$ ./configure
$ make
~~~~

The `./configure` command runs a script that is provided with the code. This
script will check properties of your system: what compilers you have, what
libraries are installed etc. You only need to run this script once after
downloading the code. Usually this script will accept arguments that can
affect how the code will be compiled for example `./configure --debug` might
build the program with debug settings enabled.

The second command `make` is what actually compiles the code. This command
expects there to be a file called `Makefile` in the current working directory.
It will read that file and then know how to compile everything. This is the
command that we run each time we want to re-compile e.g. if we've changed the
C code.

So that's the idea behind make files but how do we actually use them?

Simple make file
----------------

Create a small C program called e.g. `stuffmain.c`. This program will compile
to produce an executable called `stuff.exe`. The command to compile is `gcc
-std=c99 -Wall stuff.c -o stuff.exe`. So how do we make a make file for that.

Create a file called `Makefile`. Note that this filename must be exactly
right: is has a capital "M" but is otherwise lower-case. The file does not
have a file extension i.e. it is not called `Makefile.c` or `Makefile.txt`.
You may find that your code editor or your OS makes it difficult to create a
file without a file extension so one way is to create it as `Makefile.txt` and
then to rename it in the terminal with the `mv` ("move") command:

~~~~
$ mv Makefile.txt Makefile
~~~~

So what goes in the make file? A make file contains *rules* and to begin with
we might have only one rule like so:

~~~~
stuff.exe: stuffmain.c
	gcc -std=c99 -Wall stuffmain.c -o stuff.exe
~~~~

A rule has three components: the *target* (`stuff.exe`), the *dependencies* of
the target (`stuffmain.c`) and the command to run (`gcc ...`). The meaning of
this is that this `Makefile` defines a target called `stuff.exe` which is
generated from `stuffmain.c`. The command to generate `stuff.exe` from
`stuffmain.c` is `gcc ...`.

With this file saved in the same folder as "stuff.c" we can now invoke `make`
as

~~~~
$ make stuff.exe
gcc -std=c99 -Wall stuffmain.c -o stuff.exe
~~~~

So now that the make file remembers the command that we need to type we just
have to tell it that we want to remake `stuff.exe` and it will run that
command for us. Now see what happens if we ask it to make `stuff.exe` again:

~~~~
$ make stuff.exe
make: 'stuff.exe' is up to date.
~~~~

Somehow `make` knows that it doesn't need to recompile the code. What happens
here is that `make` looks at `stuff.exe` and its dependencies (i.e.
`stuffmain.c`) and checks the time that each file was last modified. If
`stuff.exe` is newer than `stuffmain.c` then there is no need to recompile
because `stuff.exe` is "up to date". Sometimes we want to force recompiling
just to be sure that everything is properly compiled we can do this with `make
-B`:

~~~~
$ make -B stuff.exe
gcc -std=c99 -Wall stuffmain.c -o stuff.exe
~~~~

You can see the last modified time of files in a folder by running `ls` with
the `-l` flag (although I seem to remember that it doesn't work very well in
MinGW...).

~~~~
$ ls -l
total 20
-rw-rw-r-- 1 oscar oscar   68 Feb  3 22:50 Makefile
-rwxrwxr-x 1 oscar oscar 8560 Feb  3 22:55 stuff.exe
-rw-rw-r-- 1 oscar oscar   90 Feb  3 22:50 stuffmain.c
~~~~

Missing separator
-----------------

One thing to note here about a make file is that the command is indented. It
must be indented with a proper tab character: you cannot use e.g. 4 spaces. If
(as is common for Python programmers) you have your editor configured to
insert tabs instead of spaces then you may need to disable that setting or
copy-paste a tab character from somewhere else to get this to work. My own
editor (`vim`) is clever enough to know that proper tabs are always needed in
make files even if I prefer spaces in Python files but that's not true of all
editors.

If you use spaces instead of tabs in your Makefile then you will see the
helpful error message:

~~~~
$ make stuff.exe
Makefile:2: *** missing separator. Stop.
~~~~

[//]: #**

At least `make` has told us the line (2) in the Makefile on which the error
occurs. A good way to tell the difference between tab characters and spaces is
to print the file to the terminal with `cat -A`. The `-A` flag to `cat`
makes non-displaying ASCII characters distinguishable:

~~~~
$ cat -A Makefile
stuff.exe: stuff.c$
    gcc stuff.c -o stuff.exe$
~~~~

Note that line-ending characters are now shown as `$` so that we can actually
see them. A proper tab character would show up as `^I` so we can fix the file
by deleting those spaces and inserting tabs. It now looks like:

~~~~
$ cat -A Makefile
stuff.exe: stuff.c$
^Igcc stuff.c -o stuff.exe$
~~~~

At this point running `make stuff.exe` should work.

The `all` target
----------------

Now first I said above that the expected command to compile some code is just
`make` rather than `make stuff.exe` so how do we make that happen? By default
when you simply type `make` this will try to build the special target which
is called `all`. We can add a rule for that to our Makefile:

~~~~
all: stuff.exe

stuff.exe: stuffmain.c
  gcc -std=c99 -Wall stuffmain.c -o stuff.exe
~~~~

With this in place we can now run `make`:

~~~~
$ make
make: Nothing to be done for 'all'.
~~~~

Since everything's up to date this doesn't need to recompile.

Multiple programs
-----------------

We might have a whole folder full of .c files each of which should compile to
a different .exe file. We can make a Makefile that will compile all of them
e.g.:

~~~~
all: stuff.exe foo.exe bar.exe

stuff.exe: stuffmain.c
  gcc -std=c99 -Wall stuffmain.c -o stuff.exe

foo.exe: foomain.c
  gcc -std=c99 -Wall foomain.c -o foo.exe

bar.exe: barmain.c
  gcc -std=c99 -Wall barmain.c -o bar.exe
~~~~

As you can probably guess this Makefile will compile `stuff.exe` as well as
`foo.exe` and `bar.exe`. We can use it as `make foo.exe` to make just one of
the programs or we just type `make` then it will re-compile all three
(assuming that they are not up to date). We can use a single `Makefile` to
compile as many programs as we like.

The Makefile above is a little repetitive so let's try to improve it a bit.
Firstly we're passing the same arguments to `gcc` all the time. We should
probably split that out into a variable. Conventionally this variable is
called `CFLAGS`. We can do that like so:

~~~~
CFLAGS = -std=c99 -Wall

all: stuff.exe foo.exe bar.exe

stuff.exe: stuffmain.c
  gcc $(CFLAGS) stuffmain.c -o stuff.exe

foo.exe: foomain.c
  gcc $(CFLAGS) foomain.c -o foo.exe

bar.exe: barmain.c
  gcc $(CFLAGS) barmain.c -o bar.exe
~~~~

It's still quite repetitive though. Every rule (except `all`) is of the form

~~~~
<X>.exe: <X>main.c
	gcc $(CFLAGS) <X>main.c -o X.exe
~~~~

We can have all of the rules generated by using a pattern-matched rule. To do
this we use the wildcard `%` character:

~~~~
CFLAGS = -std=c99 -Wall

all: stuff.exe foo.exe bar.exe

%.exe: %main.c
  gcc $(CFLAGS) $< -o $@
~~~~

Now when we run `make` it knows it needs to build `stuff.exe` and that it has
a pattern-rule for `%.exe` with `%` the wildcard. `stuff.exe` matches this if
`%` is replaced with `stuff`. Replacing % in the dependency as well we see
that it depends on `stuffmain.c`. So we've found a rule that matches
`stuff.exe` and `stuffmain.c`: `make` will check the dates of the files to
decide whether or not to recompile. If it does need to recompile it will
execute the command which also uses special variables. In the command `$<`
refers to the name of the source file (`stuffmain.c`) and `$@` refers to the
name of the target (`stuff.exe`).

The advantage of this pattern-matching is obvious: we can re-use one rule for
many similar files. Now if you want to add another .exe file to this Makefile
you just have to add it to the list of dependencies of the `all` target.

Summary
-------

This is just a brief explanation of how to use Makefiles for simple C
programs. `make` can do a lot more than this but it's not really needed right
now. One thing I will say though is that you can use `make` for many more
things than just C programs. For example these notes are compiled with `make`:
I write them in markdown .md files which are compiled to html using pandoc and
I have a Makefile to control that. You can also use `make` to compile a
`LaTeX` document or to control running simulations and many more things.

------------
Next section: [Loops and branching](loopsbranching.html)
