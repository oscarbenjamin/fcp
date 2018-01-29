Functions
=========

What are functions?
-------------------

You should have already seen functions in Python. The concept behind functions
in C is similar but there are some important differences. First though I want
to consider an important classification of functions that is useful in
designing good functions.

First let's think about the idea of what a *function* is. The name function
used in programming comes from Mathematics. A mathematical function is an
object which associates each value from one set of things to a value from
another set of things. We might have a function $$ f : A \to B$$ which
associates each element in the set $A$ with an element from the set $B$. For
any element $a$ from the set $A$ we can write $f(a)$ which should be the
associated element from the set $B$. The analogue of this in a computing
context is that a function is some code that has both an input and an output.
In the expression `y = f(x)` we say that `x` is an input to the function `f`
and that `y` is the resulting output.

However in programming a function can do more things than simply take an
input and return an output. A function might print something out to the
terminal or it might get some of its inputs by reading from a file. If the
function has some effect other than simply returning a value we say that this
is a *side effect* of calling the function. Examples of side effects include

- Printing output
- Changing a global variable in the program

Also the value or behaviour of some functions may depend on *external state*
rather than simply the input arguments. A function call `f(x)` might return
different values depending on something other than `x`. The external state
might be

- Input from a file or from `stdin` (i.e. typed by the user)
- A global variable in the program

Using these two properties we can distinguish 4 types of functions:

- Functions that depend on external state and also have side effects. There
  are sometimes good reasons to have such a function but this is usually to be
  avoided if possible. A random number generator is an example of this: the
  value returned when calling `rand()` depends on the hidden state of the
  generator and calling the function modifies that hidden state.

- Functions that do not depend on external state but do have side effects.
  These are output functions. `printf` is an example of such a function.

- Functions that depend on external state but do not have side effects. These
  are input functions. An example might be a function that reads a file and
  returns something based on the contents of the file.

- Functions that do not depend on external state and do not have side effects.
  These are called *pure functions*. A pure function always returns the same
  result given the same input and has no effect except for the value it
  returns. This corresponds to the mathematical idea of a function.

Ideally we want as many of our functions as possible to be pure. Pure
functions are easier to understand and less error-prone. Of course a useful
program will always need to have some side effects (such as printing output)
so we cannot make a programme entirely out of pure functions but the idea is
to have as much code as possible in pure functions. The `main` function is
generally not a pure function.

Functions in C
--------------

In C there are three parts to using a function. The function must have a
*declaration* and a *definition* and then you can have function *calls*. So
how does that look?

~~~~ C
/* square.c */

#include <stdio.h>

/* Function declaration */
int square(int x);

int main(int argc, char *argv[])
{
  int y = 4;
  int z = square(y); // Function call

  printf("%d squared is %d\n", y, z);

  return 0;
}

/* Function definition */
int square(int x)
{
  return x * x;
}
~~~~

[//]: # *]

We can run that to see

~~~~
$ make
gcc -std=c99 -Wall square.c -o square.exe
$ ./square.exe
4 squared is 16
~~~~

So we have a function `square` which is declared above the `main` function.
The definition is below the `main` function and that's fine. The important
things are that

- The function declaration needs to come before any function calls.
- The function definition needs to be consistent with the declaration.
- Any function call needs to be consistent with the declaration and the
  definition.

Before we move on let's consider all of the ways in which using a function can
go wrong.

Firstly what happens if we remove the declaration:

~~~~ C
/* square.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  int y = 4;
  int z = square(y); // Function call

  printf("%d squared is %d\n", y, z);

  return 0;
}

/* Function definition */
int square(int x)
{
  return x * x;
}
~~~~

[//]: # *]

Now if we compile that then we see

~~~~
$ make
gcc -std=c99 -Wall square.c -o square.exe
square.c: In function ‘main’:
square.c:8:3: warning: implicit declaration of function ‘square’
[-Wimplicit-function-declaration]
$ ./square.exe
4 squared is 16
~~~~

So it works but the compiler warns us about it. With another compiler this
might not compile. If the function definition was in another file then this
would be a compile error.

So what happens if instead we remove the function definition:

~~~~ C
/* square.c */

#include <stdio.h>

/* Function declaration */
int square(int x);

int main(int argc, char *argv[])
{
  int y = 4;
  int z = square(y); // Function call

  printf("%d squared is %d\n", y, z);

  return 0;
}
~~~~

Now it refuses to compile altogether:

~~~~
$ make
gcc -std=c99 -Wall square.c -o square.exe
/tmp/ccOklKbI.o: In function `main':
square.c:(.text+0x1c): undefined reference to `square'
collect2: ld returned 1 exit status
make: *** [square.exe] Error 1
~~~~

This is a funny looking error message. The message comes from late on in the
compilation process, specifically from the *linker*. The linker combines all
the bits of code in your program and expects to find the definitions for all
functions that were called in the code. This error indicates that the linker
has failed to find the code for the `square` function.

Now let's try using a definition that is inconsistent with the declaration

~~~~ C
/* square.c */

#include <stdio.h>

/* Function declaration */
int square(int x);

int main(int argc, char *argv[])
{
  int y = 4;
  int z = square(y); // Function call

  printf("%d squared is %d\n", y, z);

  return 0;
}

/* Function definition */
int square(int x, int w)
{
  return x * x;
}
~~~~

[//]: # *]

Here the definition says that this function takes two `int` arguments `x` and
`w`. However the declaration lists it as taking one `int` argument. This gives
a new compiler error

~~~~
$ make
gcc -std=c99 -Wall square.c -o square.exe
square.c:19:5: error: conflicting types for ‘square’
square.c:6:5: note: previous declaration of ‘square’ was here
make: *** [square.exe] Error 1
~~~~

[//]: # *]

When you see an error message like this look carefully at the line number
information. Here it is pointing us to lines 19 and 6 of the code file which
are the lines of the definition and declaration respectively. The error has
occurred because these two are inconsistent.

Lastly what happens if the declaration and definition are consistent but the
function call is invalid? Let's call the function as `square(y, y)` which is
invalid for a function that only takes one argument:

~~~~ C
/* square.c */

#include <stdio.h>

/* Function declaration */
int square(int x);

int main(int argc, char *argv[])
{
  int y = 4;
  int z = square(y, y); // Function call

  printf("%d squared is %d\n", y, z);

  return 0;
}

/* Function definition */
int square(int x)
{
  return x * x;
}
~~~~

[//]: # *]

Now we get a different error message again:

~~~~
$ make
gcc -std=c99 -Wall square.c -o square.exe
square.c: In function ‘main’:
square.c:11:3: error: too many arguments to function ‘square’
square.c:6:5: note: declared here
make: *** [square.exe] Error 1
~~~~

[//]: # *]

It's worth trying to understand why the compiler gives the error messages that
it does. They are hard to understand at first but if you can learn what they
mean then you can use them to help fix your code (that's the purpose of those
messages).

More functions
--------------

Let's make a function `mypow(x, p)` which computes $x^p$.

~~~~ C
int mypow(int x, int p)
{
  int power = 1;
  while(p)
  {
    power *= x;
    p--;
  }
  return power;
}
~~~~

Now we can call e.g. `mypow(2, 3)` to compute $2^3=8$. Give this a try and see
if it works. Now let's pick apart the idea of the function a bit. The function
signature has a return type (`int`) the name of the function (`mypow`) and
then lists the type and names of the arguments to the function `int x` and
`int p`. This means that the function takes two arguments both of which have
type `int`. Inside the function body we can refer to these input argument as
variables. We can modify these variables if we want (e.g. `p` in the example
above).

A function can have as many arguments as you like but it can have only one
return value. Notice that the type of the return value is fixed so it's not
possible to have a function that e.g. sometimes returns an `int` and sometimes
returns a string.

------------
Next section: [Quadratic example](quadratic1.html)
