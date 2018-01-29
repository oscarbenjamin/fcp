Exercises
=========

Loops and branching
-------------------

Write a program that can determine if a (positive) number is prime. Fill out
this skeleton program:

~~~~ C
/* primetest.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  unsigned int number = 51;

  /*
   * Insert code here
   */

  return 0;
}
~~~~

[//]: #*

Your program should print out

~~~~
$ make
gcc -std=c99 -Wall primetest.c -o primetest.exe
$ ./primetest.exe
51 is not prime
~~~~

You can test if a number $N$ is prime by checking if it is divisuble by any
number $n$ where $2 \leq n$ and $n^2 \leq N$. Try changing the value of
`number` to check that it works. Now write a program that can print out all
the prime numbers less than 100.

Write a program that can check if an integer $x$ is a root of a cubic
polynomial $$ax^3 + bx^2 + cx + d$$ where $a$, $b$, $c$ and $d$ are integers.
Fill out the skeleton

~~~~ C
/* isroot.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  int x = 5;

  int a = 1;
  int b = -4;
  int c = -7;
  int d = 10;

  /*
   * Insert code here
   */

  return 0;
}
~~~~

[//]: #*

Your program should print out something like

~~~~
$ make
gcc -std=c99 -Wall isroot.c -o isroot.exe
$ ./isroot.exe
5 is a root of 1x^3 + -4x^2 + -7x + 10
~~~~

Try different values for `x`, `a`, etc. Feel free to try and make the output
polynomial look better...

The [rational root
theorem](https://en.wikipedia.org/wiki/Rational_root_theorem) states that any
integer root of a cubic with integer coefficients divides $c$. Hence any
integer root $x$ must satisfy $-c \le x \le c$ (assuming that $c$ is
positive).  Adapt the above program so that it prints out all integer roots of
the given cubic. Test your program out with different cubics. Example output
might be

~~~~
$ ./findroots.exe
Roots of 1x^3 + -4x^2 + -7x + 10:
-2 1 5
~~~~

Functions
---------

Take the programs you wrote for the previous loop exercises and split them up
using functions. In particular create a function `isprime(n)` that returns
whether or not an integer `n` is prime.

Also make a function `polyeval(x, a, b, c, d)` that can evaluate the
polynomial $$P(x) = ax^3 + bx^2 + cx + d.$$  Then use that function to make a
function `isroot(x, a, b, c, d)` that returns whether or not `x` is a root of
$P(x)$. Finally make a function `polyprint` that can print a polynomial to
the terminal. Use all of these functions in your previous program to find the
integer roots of a cubic. Think carefully about the output of the `polyprint`
function. Does it print all polynomials the way you want it to?
