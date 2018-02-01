Command line programs
=====================

This page explains how command line programs work and how to do all the
standard things that command line programs do in C. You have already been
using the terminal and within the terminal some command line programs (`gcc`,
`make` etc). The C programs you have been making are also command line
programs. In particular our targets here are to understand

* What a command line program is
* The interface of a command line program
* Command line arguments
* Std streams `stdin`, `stdout`, `stderr`
* Return codes

Command line programs
---------------------

What are command line programs? You have already been using them: command line
programs are programs that are intended to be run in the terminal such as
`gcc` etc. These are to be contrasted with programs that have a Graphical
User Interface (GUI) such as `Notepad++`. By contrast `gcc` has a Command Line
Interface (CLI).

Our interest in CLI programs comes from the fact that they are much
easier to make than GUI programs and are also more easily *composed*. That is
to say that CLI programs can easily be combined in order to do more
complicated things. GUI programs are generally not composable because they are
difficult to automate.

Let's consider an example: suppose a GUI program provides a way to compile a
`.c` code file but in order to do so you have to click `File` then `Open` then
find the file and double-click to open it and then click the `Compile` button.
Now suppose I want to compile 1000 .c files. It is going to take me a long to
do all that clicking! On the other hand with `gcc` I can easily compile 1000
`.c` files using a loop in the shell. Suppose I have a folder full of `.c`
files. The following command will compile all of them one by one:

```bash
$ for x in *.c; do gcc -std=c99 -Wall $x -o $x.exe; done
````

It may take a while to compile them all but I can go and have a cup of coffee
instead of clicking for hours.

Of course it is possible to make a GUI program that can be told to compile
1000 .c files in a loop. The difference is that well-written CLI
programs are naturally composable so that anyone who wants to adapt them for
an unanticipated purpose in the future will be able to. The idea with command
line programs is that you just write a program that does one simple thing and
does it well. Other people can then build other programs using that simple
program - this is very similar to the idea behind using functions within
your programs.

The command line interface
--------------------------

The command line interface gives a number of ways to give input to and get
output from a program:

* Input: arguments via `argc` and `argv`.
* Input: text/data on `stdin`
* Input: environment variables (e.g. `$PATH`)
* Output: text/data on `stdout`, `stderr`
* Output: return code (success, failure)

It is also common for a CLI program to do input/output with named
files -- in that case the filenames are usually supplied as arguments to the
program.

Input arguments
---------------

The most important inputs to a CLI program are the command line
*arguments*. These are the additional words that you type on the command line
when running your program. As an example consider telling `gcc` to compile a
file called `stuff.c`. We might run

```
$ gcc stuff.c -o stuff.exe
```

In this instance we are running the program `gcc` and we are giving it 3
additional arguments `stuff.c`, `-o` and `stuff.exe`. The `gcc` program (which
is itself a C program) will receive these arguments through the `argc` and
`argv` arguments to the `main` function. When `gcc` runs it will look at these
and see that it should be compiling a file called `stuff.c` and that the
output (`-o`) executable should be saved in a file called `stuff.exe`. Having
checked that it will then go and do what we have asked it to do.

So how do we make our own program that can respond to the arguments provided
by the user on the command line? The two arguments to the main function `argc`
and `argv` provide us with a way to access the command line arguments provided
to the program. In particular `argc` is of type `int` and tells us the number
of arguments on the command line. We can make a program that prints out the
value of `argc` to test what it does:

```c
/* argcount.c */
#include <stdio.h>

int main(int argc, char *argv[])
{
  printf("argc = %d\n", argc);
  return 0;
}
```

Compiling and running we see:

```
$ gcc -Wall -std=c99 argcount.c -o argcount.exe
$ ./argcount.exe
argc = 1
$ ./argcount.exe foo
argc = 2
$ ./argcount.exe foo bar
argc = 3
$ ./argcount.exe foo bar baz
argc = 4
```

So each time we run the program we see that `argc` is different depending on
the number of arguments we gave to the program. However `argc` seems to be one
bigger than we might otherwise expect. This is because it also counts the name
of the program (`argcount.exe`) as being the first command line argument.

We can access the argument strings themselves through `argv`. The type of
`argv` is `*char[]` which I don't want to explain right now but it means an
*array of strings*. Think of this as something like a list of strings in
Python. Hence `argv[2]` gives us the 3rd argument string and we can use this
expression wherever we would otherwise use a string (e.g. in `printf`). For
example:

```c
/* argprint.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
    /* argument indices are 0 to argc-1 inclusive */
    for(int i=0; i<argc; i++)
    {
        puts(argv[i]); // puts adds a newline
    }
    return 0;
}
```

Running this (with randomly chosen arguments) we see:

```
$ ./argprint.exe foo bar baz
./argprint.exe
foo
bar
baz
```

Try these programs yourself to make sure that you understand what is
happening. At this stage we haven't covered how to work with strings properly
so it's difficult to make use of these arguments. Hopefully you can begin to
see why this is useful though: if our program takes its instructions from the
command line then we can change what our program will do without needing to
recompile it.

Reading from stdin
------------------

Another way to get input is from `stdin` or *standard input*. This corresponds
to the things that you type into the terminal while your program is running:

```c
/* getinput.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  printf("Please enter a character: ");

  /* Read one character from stdin */
  int c = fgetc(stdin);
  if( c == EOF )
  {
    fprintf(stderr, "\nSomething went wrong...\n");
    return 1;
  }

  printf("You entered '%c'\n", (char)c);
  return 0;
}
```

We haven't yet explained the `fgetc` function. It reads a single character
from a file (in this case `stdin`) and returns that character as an object of
type `int`. If there are no characters to be read from the file then it
returns the special result `EOF`.

When you run the above you should see (note that you have to type the `q` that
you can see at the end of the first line and then hit enter):

```
$ gcc -Wall -std=c99 getinput.c -o getinput.exe
$ ./getinput.exe
Please enter a character: q
You entered 'q'
```

This kind of interface is less useful than one that takes its input from the
command line though as it makes the program less composable. It is still
possible to send things to stdin but it would be better to rewrite the above
program so that you can just run it as `./getinput.exe q`.

When it is useful to read from `stdin` is if the user will want to input the
contents of a file to our program. In that case if we design a program that
reads from stdin, the user can redirect any file to become the input of our
program. Example: create a file called `a.txt` and put whatever you like in
it. Now `getinput.exe < a.txt` will show us the first character in the file:

```
$ ./getinput.exe < a.txt
You entered 'a'
```

Note that in this command the words `<` and `a.txt` will not be passed to
`./getinput.exe` as command line arguments. Those words are interpreted by the
shell: it will arrange to run `getinput.exe` with no arguments but with its
`stdin` set to be the file `a.txt`.

On the other hand if we use an empty file here then we can see what the error
code above is for:

```
$ touch empty.txt   # creates an empty file
$ ./getinput.exe < empty.txt

Something went wrong...
Please enter a character:
```

Don't worry about the fact that the messages are out of order (one is sent to
`stdout` and one to `stderr`). This is just to demonstrate that whenever we
read from a file we have to be prepared for the possibility that there won't
be anything for us to read.

Output on stdout/stderr
-----------------------

In addition to `stdin` as the standard input stream we also have two stdandard
streams for output: `stdout` (standard output) and `stderr` (standard error).
We use `stdout` for normal output when the program is working fine. We use
`stderr` to print error messages when something goes wrong. When a program is
run normally in the terminal messages printed to both `stdout` and `stderr`
will simply appear in the terminal. The difference appears when we *redirect*
either/both of them. Let's make a program that prints messages to `stdout` and
to `stderr`:

```c
/* outerr.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  printf("This is a message to stdout\n");
  fprintf(stdout, "So is this...\n");
  fprintf(stderr, "This one is for stderr\n");
  return 0;
}
```

Note that the first two lines in `main` are entirely equivalent. `printf(...)`
is precisely a convenient shorthand for `fprintf(sdtout, ...)`. This shorthand
is provided because it is so common to want to print to `stdout`. The longer
form makes it explicit though that we are asking to print to `stdout`. The 3rd
line of `main` shows that we can also print to `stderr` instead of `stdout`
(there is no conveience function for `stderr`).

If we run the program above normally then we see this:

```
$ gcc -std=c99 -Wall outerr.c -o outerr.exe
$ ./outerr.exe
This is a message to stdout
So is this...
This one is for stderr
```

On the other hand if we redirect stdout using `./outerr.exe > output.txt` then
we see this:

```
$ ./outerr.exe > output.txt
This one is for stderr
$ cat output.txt
This is a message to stdout
So is this...
```

The shell will reinterpret our command line above is asking to run
`./outerr.exe` with its `stdout` redirected to the file `output.txt`. The
words `>` and `output.txt` will not be received by `outerr.exe` as command
line arguments as these are instructions for the shell about how to run
`outerr.exe`.

When we run `outerr.exe` with its `stdout` redirected we no longer see the
messages printed to `stdout` in the terminal. Instead we only see the one
message that is printed to `stderr`. The output that was sent to `stdout` by
the program is now in the file `output.txt` (which will be created if it
didn't already exist). You can view its contents in an editor or we can print
its contents to the terminal using the `cat` command as above.

On the other hand we can redirect only `stderr` to a file using `2>` instead
of `>`:

```
$ ./outerr.exe 2> error.txt
This is a message to stdout
So is this...
$ cat error.txt
This one is for stderr
```

There are many reasons for distinguishing between `stdout` asnd `stderr`.
Here are a few:

* If `stdout` is redirected to a file then the user will probably still want
  to see error messages.
* The output on `stdout` might be enourmous in which case it might be hard to
  spot one line in the middle that shows an important error message.
* The output on `stderr` might just be an ignorable warning about a possible
  problem when the output from `stdout` might be going to a file that we want
  to use for some other purpose - mixing in the error messages with the other
  data may corrupt the file.

For simple programs the output rules are simple: all output goes to `stdout`
unless something goes wrong. When something goes wrong the program should
print a single (ideally informative) error message on `stderr` and immediately
abort.

Return codes
------------

The final piece of the command line interface that we will cover is the
*return code* (or exit code) of our program. This is the way that a program
informs us that it has ultimately succeeded or failed in what we asked it to
do. We have seen the return code in all of our programs so far: it is the
number that we return from our `main` function. Let's make a simple program
that returns different numbers depending on how many arguments it is given:

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
  if( argc != 2 )
  {
    /* error message - non-zero exit code */
    fprintf(stderr, "I want exactly one argument!\n");
    return 1;
  }
  /* Normal message - zero exit code */
  printf("Thanks for the argument.\n");
  return 0;
}
```

Now if we run this program with one argument we see:

```
$ gcc -std=c99 -Wall retcode.c -o retcode.exe
$ ./retcode.exe
I want exactly one argument!
$ echo $?
1
```

Note that when we just run a command in the shell we don't automatically see
the return code in any way. The command `echo $?` prints the return code of
the previously run program.  The `echo` command is the shell's version of a
*print* command and will print any command line arguments we give it (to its
`stdout`). The special variable `$?` is a shell variable that stores the
return code of the previously run program. Note that if we run this command
again it will show `0` since that is the return code of the `echo` command:

```
$ echo $?
0
```

If we give our program the argument it wants then we will see that it returns
0:

```
$ ./retcode.exe foobar
Thanks for the argument.
$ echo $?
0
```

So what do the return codes mean? Our programs are free to return anything
they like but the standard convention is that `0` means success and anything
non-zero indicates some kind of error.

This now completes our description of what a command-line program should do:
on error it should print an error message to `stderr` and return a non-zero
exit code (e.g. `1`). On success our program should print its result to
`stdout` (or do whatever else it does) and return `0`.

Exercises
---------

Test out the above programs and make sure you can predict what they do.

What return code do you get from `gcc` when it compiles successfully? What if
you ask `gcc` to compile an invalid program? Where does `gcc` send its output:
to `stdout` or `stderr`?

As well as getting `stdin` to read from a file or `stdout` to write out to a
file we can run a command and connet its `stdout` to the `stdin` of another
command. This is known as a *pipe* or FIFO. Use `getinput.exe` above to read
the first character printed by another command:

```
$ ls | ./getinput.exe
$ ./outerr.exe | ./getinput.exe
```

(Verify that this is getting the first character of the output of these
commands).

We can also send text directly to `stdin` using `<<<`. Compare:

```
$ ./getinput.exe <<< blah
$ ./getinput.exe < blah
```

That's all...
