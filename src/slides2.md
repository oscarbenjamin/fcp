% EMAT10006 - Further programming
% Oscar Benjamin
% 1st Feb 2016

#

## Outline

- Using the terminal
- Command line programs
- Using make files

## Advanced terminal usage

* Typing commands all of the time is tedious
* Let the terminal do the typing!
* tab, up/down, Ctrl-R, history, `!!`, `!$` and many more...

## Command line programs

* Input: arguments via `argc` and `argv`.
* Input: text/data on `stdin`

* Output: text/data on `stdout`, `stderr`
* Output: return code (success, failure)

## Input arguments

~~~~~
/* argcount.c */
#include <stdio.h>

int main(int argc, char *argv[])
{
  printf("argc = %d\n", argc);
  return 0;
}
~~~~~

~~~~~
$ gcc -Wall -std=c99 argcount.c -o argcount.exe
$ ./argcount.exe
argc = 1
$ ./argcount.exe foo
argc = 2
$ ./argcount.exe foo bar
argc = 3
$ ./argcount.exe foo bar baz
argc = 4
~~~~~

## Reading from stdin

~~~~~
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
~~~~~

~~~~~
$ gcc -Wall -std=c99 getinput.c -o getinput.exe
$ ./getinput.exe
Please enter a character: q
You entered 'q'
~~~~~

## Output on stdout/stderr

~~~~
/* outerr.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  printf("This is a message to stdout\n");
  fprintf(stdout, "So is this...\n");
  fprintf(stderr, "This one is for stderr\n");
  return 0;
}
~~~~

~~~~
$ gcc -std=c99 -Wall outerr.c -o outerr.exe
$ ./outerr.exe
This is a message to stdout
So is this...
This one is for stderr
~~~~

~~~~
$ ./outerr.exe > output.txt
This one is for stderr
$ cat output.txt
This is a message to stdout
So is this...
~~~~

## Return codes

~~~~
#include <stdio.h>

int main(int argc, char *argv[])
{
  if( argc != 2 )
  {
    fprintf(stderr, "I want exactly one argument!\n");
    return 1;
  }
  printf("Thanks for the argument.\n");
  return 0;
}
~~~~

~~~~
$ gcc -std=c99 -Wall retcode.c -o retcode.exe
$ ./retcode.exe
I want exactly one argument!
$ echo $?
1
~~~~

~~~~
$ ./retcode.exe foobar
Thanks for the argument.
$ echo $?
0
~~~~

## Summary

- command line arguments
- stdin, stdout, stderr
- return code

#

## make

Why make?

- saves typing!
- saves *remembering*! (more important)
- runs commands efficiently

## Simple makefile

~~~~
all: myprog.exe

myprog.exe: myprogmain.c
  gcc -std=c99 -Wall myprogmain.c -o myprog.exe
~~~~

The file should be called *Makefile* with a capital *M*.

The `make` command:

~~~~
$ make myprog.exe
gcc -std=c99 -Wall myprogmain.c -o myprog.exe
$ make myprog.exe
make: 'argcount.exe' is up to date.
~~~~

~~~~
$ make
make: Nothing to be done for 'all'.
$ touch argcount.c
$ make
gcc -std=c99 -Wall argcount.c -o argcount.exe
~~~~

## make demo

## How to compile Python

Download:

~~~~
wget https://www.python.org/ftp/python/3.5.1/Python-3.5.1.tgz
tar -xzf Python-3.5.1.tgz
~~~~

Compile:

~~~~
cd Python-3.5.1/
./configure
make
~~~~

Run:

~~~~
$ ./python
Python 3.5.1 (default, Jan 31 2016, 23:11:01)
[GCC 4.9.2] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
~~~~

#

## Conditionals in C

~~~~
/* conditional.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  if( argc == 1 )
  {
    printf("No arguments were given.\n");
  }
  else if( argc == 2 )
  {
    printf("One argument was given.\n");
  }
  else
  {
    printf("%d arguments were given.\n", argc - 1);
  }
  return 0;
}
~~~~

~~~~
$ make
gcc -std=c99 -Wall conditional.c -o conditional.exe
$ ./conditional.exe
No arguments were given.
$ ./conditional.exe foo
One argument was given.
$ ./conditional.exe foo bar
2 arguments were given.
$ ./conditional.exe foo bar baz
3 arguments were given.
~~~~

## while loops in C

~~~~
/* while.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  int x = 3;
  while( x != 0 )  /* while( x ) */
  {
    printf("x = %d\n", x);
    x--;
  }
  printf("finished\n");
  return 0;
}
~~~~

~~~~
$ make
gcc -std=c99 -Wall while.c -o while.exe
$ ./while.exe
x = 3
x = 2
x = 1
finished
~~~~

## for loops in C

~~~~
/* for.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  for(int i=0; i<3; i++)
  {
    printf("i = %d\n", i);
  }
  printf("finished\n");
  return 0;
}
~~~~

~~~~
$ make
gcc -std=c99 -Wall for.c -o for.exe
$ ./for.exe
i = 0
i = 1
i = 2
finished
~~~~

## break and continue

~~~~
/* breakcontinue.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  for(int i=0; i<10; i++)
  {
    if(i == 3)
    {
      continue;
    }
    printf("%d ", i);
    if(i == 6)
    {
      break;
    }
  }
  puts("");
  return 0;
}
~~~~

~~~~
$ make
gcc -std=c99 -Wall breakcontinue.c -o breakcontinue.exe
$ ./breakcontinue.exe
0 1 2 4 5 6
~~~~

## functions

~~~~
/* functions.c */

#include <stdio.h>

/* function declaration */
int factorial(int n);

int main(int argc, char *argv[])
{
  for(int i=0; i<20; i++)
  {
    /* function call */
    printf("%d! = %d\n", i, factorial(i));
  }
}

/* function definition */
int factorial(int n)
{
  int product = 1;
  for(int i=2; i<=n; i++)
  {
    product *= i;
  }
  return product;
}
~~~~

## functions output

~~~~
$ ./functions.exe
0! = 1
1! = 1
2! = 2
3! = 6
4! = 24
5! = 120
6! = 720
7! = 5040
8! = 40320
9! = 362880
10! = 3628800
11! = 39916800
12! = 479001600
13! = 1932053504
14! = 1278945280
15! = 2004310016
16! = 2004189184
17! = -288522240
18! = -898433024
19! = 109641728
~~~~

##

~~~~
4005d0 <factorial>:
4005d0:  83 ff 01                cmp    $0x1,%edi
4005d3:  7e 1f                   jle    4005f4 <factorial+0x24>
4005d5:  83 c7 01                add    $0x1,%edi
4005d8:  ba 02 00 00 00          mov    $0x2,%edx
4005dd:  b8 01 00 00 00          mov    $0x1,%eax
4005e2:  66 0f 1f 44 00 00       nopw   0x0(%rax,%rax,1)
4005e8:  0f af c2                imul   %edx,%eax
4005eb:  83 c2 01                add    $0x1,%edx
4005ee:  39 fa                   cmp    %edi,%edx
4005f0:  75 f6                   jne    4005e8 <factorial+0x18>
4005f2:  f3 c3                   repz retq
4005f4:  b8 01 00 00 00          mov    $0x1,%eax
4005f9:  c3                      retq
4005fa:  66 0f 1f 44 00 00       nopw   0x0(%rax,%rax,1)
~~~~

~~~~
int factorial(int n)          /*  %edi : n       */
{
  int product = 1;            /*  %eax : product */
  for(int i=2; i<=n; i++)     /*  %edx : i       */
  {
    product *= i;
  }
  return product;
}
~~~~

#

## Assignment 1

Assignment 1 is up on Blackboard waiting for you.

There's a script you want to use (DEMO)

# That's all folks...

(See you on Friday)
