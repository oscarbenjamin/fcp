Basic C
=======

Another look at hello world
---------------------------

In the last section we saw how to save our C code in a file and how to compile
and run that code using the terminal. We're now in a position to start
thinking about the C code itself.  First things first let's have another look
at the hello world C program that we wrote in the previous section:

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
    puts("Hello world!");
    return 0;
}
```

Now let's go through this from the top. The first line is `#include <stdio.h>`
and is an example of a `#include` statement. This is similar to an `import`
statement in Python and is used to reference functions and variables from
other code files. In this case we want to be able to use the `puts` function
which is declared in `stdio.h` so we need to `#include` this for that reason.
Files ending with `.h` also contain C code and are called *header files*. We
will look more at these later. For now just know that we will probably always
need to have the line `#include <stdio.h>` at the top of our C programs.

The rest of the code is for the `main` function. This is made up of a
*signature line* `int main(int argc, char *argv[])` and a *function body*
which is enclosed in a pair of curly brackets `{` and `}`. We will look more
at how to write functions later but for now just know that this defines the
`main` function.  Every C program needs to have a `main` function. When we run
the program from the terminal it is the `main` function that is called and it
is the code in the body of the main function that is executed.

In the body of the `main` function there are two statements on two different
lines. The end of a statement is marked with a `;` in C. The first statement
is `puts("Hello world!");`. This equivalent to the Python statement
`print("Hello world!")`. The `puts` function is the function for printing a
string in C. Like in Python we can make a string using double quotes `"`.
Unlike in Python we cannot use single quotes `'` here and the statement needs
to be terminated with a semicolon.

Finally the last statement in the body of the `main` function is `return 0`.
This sets the *exit code* of the program (more on this later). For now just
know that we always need to return an integer value from the `main` function
and this statement is the syntax for doing that.

Variables in C
--------------

So the basic idea that we get above is that our C program has to have a bunch
of stuff just to get it to work and that the code that we want to run needs to
go inside the `main` function. So how can we start to put some interesting
things in there? Suppose we have a Python script that looks like:

~~~~~~~~ python
a = 20
b = 30
c = a * b
print("c = %d" % c)
~~~~~~~~

Assuming I saved the above as `variables.py` I can open a terminal, `cd` into
the appropriate directory, and run it like so:

~~~~~~
$ python variables.py
c = 600
~~~~~~

Here we have three variables `a`, `b`, and `c`. The first statement assigns
the value `20` to the name `a`. The second statement assigns the value `30` to
the name `b`. The third statement computes the result `a * b` and assigns that
value to the name `c`. Finally we print a message that shows the value of `c`.
Hopefully that's all straight-forward. So how do we do the same in C?

~~~~~~~~ c
#include <stdio.h>

int main(int argc, char *argv[])
{
    int a;
    int b;
    int c;
    a = 20;
    b = 30;
    c = a * b;
    printf("c = %d\n", c);
    return 0;
}
~~~~~~~~

[//]: # *]

I'll save the above code as `variables.c`. To compile and run it open a
terminal, `cd` into the appropriate directory and do

~~~~~~~~~
$ gcc variables.c -o variables.exe
$ ./variables.exe
c = 600
~~~~~~~~~

Hopefully this is broadly self-explanatory. Remembering the hello world
program we can see that we have the same basic stuff in this program. We first
`#include <stdio.h>` and then we have the `main` function with its signature
line and the opening and closing curly braces and `return 0`. The code that
actually executes in a C program is the code in the body of the `main`
function before the `return 0;` statement.

Firstly, in order to have three variables `a`, `b`, and `c` in a C program we
need to declare the variables. The line that says `int a;` is the
*declaration* for variable `a`. It declares that there will be a variable
named `a` that will be of a particular *type*, specifically `int`. The name
`int` is short for *integer* and we'll explain more about what `int` really
means in C later. Having declared the variable `a` we can later give it a
value with the statement `a = 20;`. The first statement that gives a value to
a variable is said to *initialise* the variable. The last statement in this
program uses the `printf` function which prints out the value of the variable
`c` (we'll come back to `printf` later).

In Python we don't declare variables and just skip straight to initialising
them. In Python objects also have types but we don't need to declare the type
of object that a variable can have. We can see the type of an object in Python
using the `type` function:

~~~~~~~~
$ python
>>> a = 1
>>> type(a)
<type 'int'>
>>> b = 'hello'
>>> type(b)
<type 'str'>
~~~~~~~~

So in Python we can just write `a = 1` and set the variable `a` to be an
object of type `int` having the value `1`. In C we first have to tell the
compiler what the type of a variable `a` should be before we can set it to any
value. Now the C program above declares all of its variables at one place at
the top of the `main` function but that's not the only way to do it. We can
make it a bit shorter. For example we can declare all three variables on one
line like this:

~~~~~~~~ c
#include <stdio.h>

int main(int argc, char *argv[])
{
    int a, b, c;
    a = 20;
    b = 30;
    c = a * b;
    printf("c = %d\n", c);
    return 0;
}
~~~~~~~~

[//]: # *]

In our C program the line `int a, b, c;` is equivalent to the three separate
statements `int a;` etc. This is a handy way of declaring lots of variables
without using too much screen space. We can also declare each variable at the
same time as initialising it e.g.:

~~~~~~~~ c
#include <stdio.h>

int main(int argc, char *argv[])
{
    int a = 20;
    int b = 30;
    int c = a * b;
    printf("c = %d\n", c);
    return 0;
}
~~~~~~~~

[//]: # *]

However what we must not do is try to use a variable before it is declared or
initialised. The compiler reads the code in order from start to finish. If we
refer to a variable before it is declared then the compiler will refuse to
compile it. So changing the above to

~~~~~~~~ c
#include <stdio.h>

int main(int argc, char *argv[])
{
    int c = a * b; /* before declaration */
    int a = 20;
    int b = 30;
    printf("c = %d\n", c);
    return 0;
}
~~~~~~~~

[//]: # *]

we now get

~~~~~~~~
$ gcc variables.c -o variables.exe
variables.c: In function ‘main’:
variables.c:5:13: error: ‘a’ undeclared (first use in this function)
variables.c:5:13: note: each undeclared identifier is reported only once for each function it appears in
variables.c:5:17: error: ‘b’ undeclared (first use in this function)
~~~~~~~~

There are two types of messages that the compiler can give us: *errors* and
*warnings*. Both type of message indicates a problem with our code. The
difference is that if there are *any* errors then the code will not be
compiled. In other words the file `variables.exe` will not have been created
(or if it already exists then it won't have been modified).

If instead we declare `a` and `b` before using them but don't initialise them
until after we get a warning.

~~~~ c
#include <stdio.h>

int main(int argc, char *argv[])
{
    int a, b;
    int c = a * b; /* before initialisation */
    a = 20;
    b = 30;
    printf("c = %d\n", c);
    return 0;
}
~~~~

[//]: # *]

Now we get

~~~~
$ gcc variables.c -o variables.exe
$ ./variables.exe
c = 0
~~~~

But that's totally wrong. How did `c` get the value `0`. What's happened is
that the compiler has implicitly set the values of `a` and `b` to `0` when
they were declared. This is not guaranteed to happen though so this program
could potentially print any value.

Annoyingly the compiler didn't give us any warnings or errors about this.
If we want to see the warnings then we need to ask the compiler to turn them
on by giving it the `-Wall` argument like so

~~~~
$ gcc -Wall variables.c -o variables.exe
variables.c: In function ‘main’:
variables.c:6:9: warning: ‘a’ is used uninitialised in this function [-Wuninitialized]
variables.c:6:9: warning: ‘b’ is used uninitialised in this function [-Wuninitialized]
~~~~

Always compile all of your code with the warnings turned on and always fix all
warning messages. The warnings means that something is wrong in your code.
Don't ignore them!

Changing variables
------------------

Just like in Python we can change the value of a variable by writing e.g. `x =
x + 2` or in shorthand `x += 2`. Unlike in Python we also have the increment
and decrement operators `x++`, `++x`, `x--`, and `--x`. The `++` operators are
a short form for `x += 1` and the `--` operators are for `x -= 1`. So we have

~~~~ c
#include <stdio.h>

int main(int argc, char *argv[])
{
  int a = 2;
  printf("a = %d\n", a);
  a = a + 4;
  printf("a = %d\n", a);
  a += 4;
  printf("a = %d\n", a);
  a++;
  printf("a = %d\n", a);
  --a;
  printf("a = %d\n", a);
  return 0;
}
~~~~

[//]: # *]

This gives

~~~~
$ gcc -Wall change.c -o change.exe
$ ./change.exe
a = 2
a = 6
a = 10
a = 11
a = 10
~~~~

So we can see that the value of the variable changes with each of these
statements. As you can expect the output of this program shows that each
statement is executed in order one after another just like in Python (there
are some programming languages that don't behave this way).

The `printf` function
---------------------

In the last section we have used the `printf` function to print output rather
than the `puts` function that we used before. These functions are similar but
not the same. `puts` is short for "PUT String" and just prints a string to the
terminal. `printf` is short for "PRINT Formatted" (or something like that) and
is used to print the formatted value of a variable. The specific line that we
used above is

~~~~~~~~ c
    printf("c = %d\n", c);
~~~~~~~~

The equivalent Python code is

~~~~~~~~ python
print("c = %d" % c)
~~~~~~~~

I'm hoping that you've already seen this %-style formatting in Python so that
it's easy to understand how the C version works. Essentially the idea is that
we have a format string e.g. `"c = %d"` which has certain variable place-holders
in it e.g. the `%d` part. Here `%d` means format an integer using *Decimal*
digits. So the Python code `"c = %d" % c` results in the string "c = 600"
because `"600"` is the decimal digit representation of the value of the
variable `c`. In Python we can then print that string with the `print`
function.

The way it works in C is slightly different: we have one function called
`printf` that takes the format string i.e. `"c = %d\n"` and any variables to
be formatted (i.e. `c` in the example) and prints the result straight out to
the terminal. So this results in a string like `"c = 600\n"` being printed.

You may be wondering what the `\n` in the string does. This is a *newline*
character. It is used to mark the end of a line of text. The terminal knows
when it sees this that any new things that will be printed should be printed
on the line below. When we use the `print` function in Python or the `puts`
function in C the newline character is added automatically which is why if you
call `print` or `puts` twice then the different strings will show on different
lines in the terminal. With `printf` this newline character is not
automatically added so we need to include it in the string to be printed. The
symbol for a newline character in a string (in C or Python) is `\n`. Although
this looks like two characters it's really just one special character.

To see the effect of newline characters try modifying the hello world program
to use `printf` like this

~~~~~~~~~~ c
#include <stdio.h>

int main(int argc, char *argv[])
{
    printf("Hello world!");
    return 0;
}
~~~~~~~~~~

[//]: # *]

Exercises
---------

Now try changing the string to `"Hello world!\n"`, `"Hello\nworld!"`, or
`"Hello\nworld!\n"` and see what happens. What happens if you call
`puts("Hello")` followed by `puts("World")`? What if you use two `printf`
calls instead of `puts`?

Comments and coding style
-------------------------

In C as in Python we normally use comments to help explain the meaning of our
code. For example:

~~~~~~~~~~ c
/*
 * hello.c : prints a nice greeting
 *
 * Author: Oscar Benjamin
 * Date: 29 Jan 2016
 */
#include <stdio.h>

int main(int argc, char *argv[])
{
    /* Actually print the message. */
    printf("Hello world!");

    return 0; // Never forget to return 0!
}
~~~~~~~~~~

[//]: # *]

There are two types of comments. Double slash `//` comments are similar to
Python and mean that from that point to the end of the line is a comment that
will be ignored by the compiler. The other kind of comment begins with `/*`
and ends with `*/` and can span multiple lines. It is common to put a big
comment at the start that is nicely formatted with a `*` at the beginning of
each line. In small examples in these notes I will use a comment at the start
to indicate the name of the file (so that you understand which file I'm
compiling later).

In Python indentation defines the beginning and end of blocks e.g. in an `if`
block or a `for` loop. In C blocks begin and end with curly brackets e.g. `{`
and `}` so the indentation is unnecessary. Also since a semicolon `;` is used
to mark the end of statements it's not important for them to be on one line as
it is in Python. This means that we could write the code above like this:

~~~~~~~~~~ c
#include <stdio.h>
int main(
int argc, char *          argv[]) {
printf(
"Hello world!"
)
; return 0; }
~~~~~~~~~~

However this code is much harder to read so please don't do that! Always use
indentation consistently as it makes it much easier to read your code. Also
make sure that you keep your indentation correct at all times. Your
programming editor will be able to handle a lot of the indentation for you but
only if you keep it correct. Please don't think of indentation as being
something that you should fix at the end; keep your code clean and easy to
read at all times. (How you write your code is part of the markscheme in this
unit.)

------------
That's all for now... Coming soon: [elementary types](elementary_types.html)
