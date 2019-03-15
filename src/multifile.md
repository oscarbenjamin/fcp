Using multiple files
====================

Larger C programs (as with any language) should be spread across multiple
files. So far all of our work has been in a single .c file. Here we will see
how to split our code across different files.

Functions
---------

The basic unit of code in C is a function. A single function must be defined
in a single .c file. However a larger program can be split into many functions
and we can put different functions in different files.

Let's take a quick example program:

~~~~ C
/*
 * factmain.c
 *
 * Main code file for the func program.
 */

#include <stdio.h>
#include <stdlib.h>

/* Function declaration */
int factorial(int n);

int main(int argc, char *argv[])
{
  /* Get the checking out the way first */
  if(argc != 2) {
    fprintf(stderr, "Usage: ./func.exe N\n");
    return 1;
  }
  /* We know argc == 2 so argv[1] exists */
  char *nstr = argv[1];
  int n = atoi(nstr);

  /* Note atoi returns 0 for error */
  if(n <= 0) { // More checking needed for e.g. "123asd"
    fprintf(stderr, "N should be a positive integer\n");
  }

  /* Function call */
  printf("factorial(%d) = %d\n", n, factorial(n));
  return 0;
}

/* Function definition */
int factorial(int n)
{
  int nfact = 1;
  while(n) {
    nfact *= n;
    n--;
  }
  return nfact;
}
~~~~

Remember that there are 3 parts to using a function in C:

  * The declaration
  * The definition (where the actual code goes)
  * The call (where the function is *used*)

A function in its own file
--------------------------

So how do we split this into multiple files? We need 3 files for this. In
addition to `factmain.c` we will have `factorial.h` and `factorial.c`. The .h
file is a *header* file and is for function declarations. The file
`factorial.c` will have the *definition* of the factorial function. Then we
will have `factmain.c` which has the `main` function of the program. Every
program must have a single main function: that is the entry point for the
execution of the program.

So the above example becomes three files. Firstly there is `factorial.h`:

~~~~ C
/* factorial.h */

/* Function declaration */
int factorial(int n);
~~~~

Then there's `factorial.c`:

~~~~ C
/* factorial.c */

/* Not needed in this example but usually X.c imports X.h */
#include "factorial.h"

/* Function definition */
int factorial(int n)
{
  int nfact = 1;
  while(n) {
    nfact *= n;
    n--;
  }
  return nfact;
}
~~~~

Now `factmain.c` looks like:

~~~~ C
/*
 * factmain.c
 *
 * Main code file for the func program.
 */

#include <stdio.h>
#include <stdlib.h>

/* Including factorial.h makes the factorial function available */
#include "factorial.h"

int main(int argc, char *argv[])
{
  /* Get the checking out the way first */
  if(argc != 2) {
    fprintf(stderr, "Usage: ./func.exe N\n");
    return 1;
  }
  /* We know argc == 2 so argv[1] exists */
  char *nstr = argv[1];
  int n = atoi(nstr);

  /* Note atoi returns 0 for error */
  if(n <= 0) { // More checking needed for e.g. "123asd"
    fprintf(stderr, "N should be a positive integer\n");
  }

  printf("factorial(%d) = %d\n", n, factorial(n));
  return 0;
}
~~~~

Now to compile this we need to tell the compiler to compile both of the .c
files (not the .h file):

~~~~ C
$ gcc factmain.c factorial.c -o fact.exe
$ ./fact.exe 3
factorial(3) = 6
~~~~

The idea here is that when we tell the compiler to compile a bunch of .c files
the compiler will take all that code and put it together to make a single
program. To understand why we have to write it the way we did above consider
these points:

  * There are two stages to compiling an executable: compiling and linking.
    The compile stage applies to each .c file separately and compiles each to
    an "object file". The linker then takes all of the object files and
    combines them into a single executable.

  * When the compiler compiles a .c file it doesn't look in the other .c
    files. If the code in that file calls a function defined in another .c
    file then that's fine provided the compiler has seen a declaration of that
    function. The compiler just needs to know the types of all the arguments
    and the return type of the function so that it can generate the correct
    machine code to *call* that function.

  * When the compiler finds a `#include` statement it goes and reads all of
    the code in the corresponding .h file. Those .h files should contain the
    declarations of all the functions used in the .c file that is being
    compiled.

  * After compiling all of the .c files separately the compiler *links* them.
    In this step it expects that all the functions that were declared will
    have be found in exactly one of the object files. It also expects to find
    a `main` function somewhere which it uses as the entry point to the
    program.

When we define our own .h files we need to `#include` them with double quotes
(`"`) e.g.

~~~~ C
#include <stdio.h>

#include "factorial.h"
~~~~

In simple terms the double quotes tell the compiler to look for the .h file
*here*. The .h files from the C standard library are stored somewhere else:
The compiler knows where to find them as long as we use the angle bracket
notation `<stdio.h>`.


Download the code
-----------------

Code is here: [Fact 1.0](examples/fact-1.0.zip)


General code setup
------------------

So the general idea is that we have a bunch of files containing C code. Some
are .c files and some are .h files. We might for example have:

~~~~
factorial.h
factorial.c
squareroot.h
squareroot.c
exponential.h
exponential.c
calcmain.c
rpnmain.c
Makefile
~~~~

The idea is that the bulk of our code is in functions in the various .c files.
Those functions are defined in the corresonding .h files. Then we separately
have files such as `calcmain.c` and `rpnmain.c` that have `main` functions in.
Thos files don't need correspondibg .h files because we would never `#include`
them in another program.

The setup described here would create two programs `calc.exe` and `rpn.exe`.
Each of them would use some of the other .c and .h files. Our `Makefile`
should look something like

~~~ Makefile
all: calc.exe rpn.exe

calc.exe: calcmain.c factorial.h factorial.c squareroot.h squareroot.c exponential.h exponential.c
	gcc calcmain.c factorial.c squareroot.c exponential.c -o calc.exe

rpn.exe: rpnmain.c factorial.h factorial.c squareroot.h squareroot.c exponential.h exponential.c
	gcc rpnmain.c factorial.c squareroot.c exponential.c -o rpn.exe
~~~

A better way to write that is

~~~ Makefile
CFILES =\
	factorial.c\
	squareroot.c\
	exponential.c

HFILES =\
	factorial.h\
	squareroot.h\
	exponential.h

all: calc.exe rpn.exe

calc.exe: calcmain.c $(CFILES) $(HFILES)
	gcc calcmain.c $(CFILES) -o calc.exe

rpn.exe: rpnmain.c $(CFILES) $(HFILES)
	gcc rpnmain.c $(CFILES) -o rpn.exe
~~~~

The idea here is that each `*main.c` files just contains a `main` function and
all the other code is in other files. The convention is that e.g. `rpn.exe`
has it's `main` function in the file `rpnmain.c`.


