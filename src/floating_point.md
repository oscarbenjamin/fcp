Floating point
==============

This page discusses the use of floating point data types in C. Floating point
is a way of representing non-integer numbers commonly used in programming.
Many of the details here are applicable to the majority of programming
languages (e.g.  Python). Python's `float` datatype is the same as the
`double` datatype used in C.

Decimal floating point
----------------------

We have seen that in C we can represent integers using e.g. the `int` data
type which is a 32-bit binary representation of an integer. It is easy to see
how, working in base 2, the integer 11 (for example) can be represented as 1011.
So how can we represent non-integer numbers? The first question we need to ask
is: what kind of non-integer numbers do we want to represent?

The integers $\mathbb{Z}$ are a subset of the rational numbers $\mathbb{Q}$.
In turn the rationals $\mathbb{Q}$ are a subset of the real numbers
$\mathbb{R}$ which are a subset of the complex numbers $\mathbb{C}$. In
scientific work we often want to work with real numbers but there is no way to
exactly represent such numbers in binary. The most common way to approximate
real numbers is by using floating point.

Consider an example in decimal. We represent non-integer numbers using decimal
by writing e.g. $123.4$ which means
$$
    1 \times 100 + 2 \times 10 + 3 \times 1 + 4 \times \frac{1}{10}
$$
That is the numbers after the decimal point are the coefficients for the
*negative* powers of 10. In *standard form* we would write this number as
$$
    1.234 \times 10^2
$$
using 1 digit before the decimal point. In this form we say that the first
part $1.234$ is the *mantissa* and that the *exponent* is $2$. In general we
can write any such number using an integer mantissa e.g.
$$
    1234 \times 10^{-1}
$$
and since we know how to write integers in binary this is a clue to how we
will write non-integers in binary: using an integer mantissa and exponent.
With a bit of thought we can see that any such number will always be a
rational number. We cannot exactly represent any irrational number in this way
but using a lot of digits in the mantissa we can get close. For example
$$
    \sqrt{2} \approx 14142135623730952 \times 10^{-16}.
$$

Rounding
--------

When working formally with *floating point* arithmetic we will usually define
a fixed number of digits to work with and then ensure that all of our
calculations are rounded to that number of digits. Suppose that we are working
with decimal floating point using 2 digits and want to calculate $120 \times 120$.
We can write 12 in standard form and then use integer addition and
multiplication to compute the result:
$$
    120 \times 120 = 12 \times 10^1 \times 12 \times 10^1 = 144 \times 10^2
          \approx 14 \times 10^3
$$
Notice that in the last step of our floating point calculation we round the
result to the nearest 2-digit decimal floating point number. As a result our
final computer value is not exactly correct: we have 14000 instead of the
correct answer which is 14400. The fact that our answer is only approximately
correct is a general feature of floating point.

The difference between the our rounded result and the exact result is called
the rounding error and is in this case $-400$ and has a magnitude around 1% of
the result. This is to be expected when using 2-digit decimal floating point.
When using 3-digit decimal floating point there would be no error in this
example but other calculations would have errors on the order of 0.1% of each
result. For practical purposes we would often want to make the error much
smaller than this so something like 15 digits would be typical for a computer
program.

Notice that some relatively simple numbers cannot be represented in decimal
floating point no matter how many digits we use:
$$
    \frac{1}{3} = 0.3333333333333333\cdots
$$
In general a rational number $\frac{n}{d}$ can be represented exactly with a
finite number of decimal digits of the denominator is only divisible by $2$ or
$5$. Note though that any number that we write in decimal e.g. $0.1$ can be
exactly represented in decimal floating point (this is not true for binary
floating point).

Binary floating point
---------------------

The above has all been described in terms of decimal floating point because it
is more familar and so easier to understand. Many computing environments (e.g.
Python) do provide libraries for using decimal floating point but the default
types (e.g. `float` in Python) always to use binary floating point. We can
extend our binary digit notation to non-integers in the obvious way so that
e.g. the number 1.25 is written as
$$
    1.01 = 1 \times 1 + 0 \times \frac{1}{2} + 1 \times \frac{1}{4}.
$$
Any number that we can write in this way using binary digits can be written
with some integer mantissa $m$ and exponent $e$ as
$$
    m \times 2^e.
$$
As for decimals this can only exactly represent particular rational numbers:
those with denominators that are powers of 2. What this means is that some
fairly innocent looking numbers will not be represented exactly: the number
0.1 for example has the recurring binary representation
$$
    \frac{1}{10} = 0.0001100110011\cdots
$$
This leads to a lot of confusion around floating point arithmetic since
calculations appear to be "inexact" in some sense. Because of this and also
because of the rounding errors in arithmetic floating point numbers are
usually treated as being *imprecise*. So for example we wouldn't assume that
the result of a calculation would be exactly correct but rather comes with
some (hopefully) small error.

IEEE 64-bit floating point
--------------------------

Python's `float` type and C's `double` type both use the IEEE 64-bit floating
point standard. This prescribes a way to represent a binary floating point
number using 1 sign bit (indicating whether the number is positive or
negative), a 53 bit mantissa and 11 bits for the exponent. Since the leading
digit of the mantissa is always 1 we don't need to store it so the mantissa
takes up 52 bits of space. Altogether storing a number this way therefore
takes $52+11+1 = 64$ bits of ($8$ bytes) of space.

We can see this type in action in Python:

```python
>>> x = 1.0 / 3.0
>>> x
0.3333333333333333
>>> type(x)
<class 'float'>
```

Here we can see that Python choose to show us 16 digits of the number. This is
because 16 decimal digits is approximately the same precision as 53 binary
digits:

```python
>>> 10**16 / 2**53
1.1102230246251565
```

The effect of rounding error can be seen in something like

```
>>> (0.11 - 0.1) - 0.01
-5.204170427930421e-18
>>> 0.11 - (0.1 + 0.01)
0.0
```

which also shows that the order of arithmetic can be important in floating
point. Note that e.g. `-5.2e-18` is a shorthand for $-5.2 \times 10^{-18}$.
The error here is around $10^{-18}$ which is typical for 64-bit floating
point.

We can use Python's decimal module to see the exact value of a binary `float`
which can have a very long decimal representation:

```python
>>> y = 0.1
>>> y
0.1
>>> import decimal
>>> decimal.Decimal(y)
Decimal('0.1000000000000000055511151231257827021181583404541015625')
```

Th decimal output shows that normally when we print the value of a binary
`float` in decimal the result is rounded on *output* (which adds to the
confusion around the errors in floating point).

The 53 bit mantissa means that we can use binary floating point to exactly
represent any integer up to `2**53` but then other things happen:

```python
>>> z = 2.0 ** 53
>>> z
9007199254740992.0
>>> z + 1
9007199254740992.0
>>> z == z + 1
True
```

This happens because exactly representing $2^{53} + 1$ would need 54 binary
digits so the expression `z + 1` gets rounded down.

There is also a limit on the number of bits used for the exponent so that
large numbers eventually get too big. In the opposite direction small numbers
can become so small that they are just treated as zero:

```python
>>> x ** 123
1e+123
>>> x ** 1240
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
OverflowError: (34, 'Numerical result out of range')
>>> x**-123
1e-123
>>> x**-1234
0.0
```

The notation `1e+123` is a short-hand way of printing a `float` which a
large/small exponent and represents $1\times 10^{123}$.

The only other feature worth mentioning is that there are a few special
floating point values representing things like positive and negative infinity
and the special `NaN` (not a number) value which represents the result of
invalid calculations:

```python
>>> w = float('inf')
>>> w
inf
>>> -w
-inf
>>> w - w
nan
```

Floating point in C
-------------------

In C we usually do binary floating point with the `double` datatype. This is
shorthand for *double precision* and reflects the fact that C also has a
`float` datatype that only uses 32 bits instead of 64. We can see this type in
action here:

```C
/* double.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  double x = 1 / 3.0;

  printf("x = %f\n", x);
  printf("x = %.10f\n", x);
  printf("x = %.20f\n", x);

  return 0;
}
```

Running this gives

```
$ make
gcc -std=c99 -Wall double.c -o double.exe
$ ./double.exe
x = 0.333333
x = 0.3333333333
x = 0.33333333333333331483
```

Note that when printing a variable of type `double` we need to use the `%f`
format code instead of the `%d` code. This is `f` for *floating point*.
We can specify the number of decimal places to print with e.g. `%.10f` for 10
d.p. Notice that if we try to print too many digits then gibberish comes out.
This is because `x` does not hold the *exact* value $\frac{1}{3}$ which cannot
be exactly represented in binary floating point.

Mathematical functions
----------------------

Part of the point of using floating point is to be able to do scientific
calculations so naturally we have all of the usual scientific functions, like
$\sin$, $\cos$ etc. In Python these are found in the `math` module and in C we
get them from the `math.h` header. This shows how to compute the square root
of a number in binary floating point:

```C
/* sqrt.c */

#include <stdio.h>
#include <math.h>  // for the sqrt function

int main(int argc, char *argv[])
{
  double y = 2;

  double sqrty = sqrt(y); // call sqrt

  printf("sqrt(%f) = %f\n", y, sqrty);

  return 0;
}
```

To compile this we need to tell `gcc` to link with the math library which we
do by giving an additional argument `-lm` to the compiler e.g.:

```
$ make
gcc -std=c99 -Wall sqrt.c -o sqrt.exe -lm
$ ./sqrt.exe
sqrt(2.000000) = 1.414214
```

Exercises
---------

We can also print floating point numbers in different formats using e.g. the
`%e` or `%g` codes (try these out with both large and small numbers.) What
does `%3.3e` do?

The `math.h` header also defines other functions that we can use e.g. `sin`,
`cos(x)`, `exp(x)`, `pow(x, y)`, `log(x)`, etc. Try them out. Do they use
degrees or radians? What base does `log` use? What does `log(-1)` or
`sqrt(-1)` give?
