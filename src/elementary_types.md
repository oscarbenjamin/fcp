Elementary types
================

Bits and bytes
--------------

In the previous section we saw how to declare variables of type `int`. Let's
now try to understand a little more about what this means. So `int` is short
for *integer*. Hopefully you know what an integer is i.e. it's a type of
number that is a whole number such as 0, 1, 2, ..., -1, -2, ... etc. Now a
computer represents all data and all variables using bits: individual 1s and
0s. So how exactly does that work?

A bit is a single *binary digit* which can be a 1 or a 0. In decimal
we have 10 digits 0, 1, 2, ..., 9 and to write down an integer larger than 9
we use several of these digits together e.g. 1024. A computer represents
integers in the same way using binary digits so for example 1024 is written as
10000000000 (that's a 1 with 10 zeros after it). The idea is the same as when
working in base 10 except that there's no higher digit than 1 so when counting
it looks like 0, 1, 10, 11, 100, 101, 110, 111, 1000, ... To interpret a
number written in binary form let's first consider how to interpret a number
in decimal form e.g.:
$$
   123 = 1 \times 100 + 2 \times 10 + 3 \times 1
$$
Each digit in of a number written in decimal form is the coefficient for a
different power of 10 with the powers increasing from right to left. In the
same way given a binary number we have
$$
    0\mathrm{b}11001 = 1 \times 16 + 1 \times 8 + 0 \times 4 + 0 \times 2 + 1 \times 1
            = 25
$$
Here I'm using the $0\mathrm{b}$ prefix to indicate that a number is
represented in binary form (otherwise 11001 looks like eleven thousand and
one). So we can see here that each digit in the binary representation of an
integer is a coefficient for a power of 2 instead of 10. The powers of 2 come
up a lot in computing and should hopefully look familiar: 1, 2, 4, 8, 16, 32,
64, 128, 256, 512, 1024, etc.

Another representation of integers that is common in programming is to
represent the number in *hexadecimal* form. In hexadecimal we have 16 digits
which go from 0, 1, 2, ..., 9, A, B, C, D, E, F. We will prefix numbers in
hexadecimal form with $0\mathrm{x}$ to avoid ambiguity. In this way we have
that $15 = 0\mathrm{xF}$.  In hexadecimal form 123 would be written as
$$
    123 = 0\mathrm{x7B} = 7 \times 16 + 11 \times 1
$$
so that each digit is a coefficient for a power of 16. One hexadecimal digit
is equivalent to 4 binary digits so two hexadecimal digits is just right for 1
byte. This means that we can write any byte with a 2 digit hexadecimal number.
This is a handy trick that is often used to shorten long binary strings in
programming.

The easiest way to see the binary or hex representation of an integer is using
the Python `bin` and `hex` functions which return the representation as a
string:

~~~~~ python
$ python
...
>>> bin(25)
'0b11001'
>>> bin(1024)
'0b10000000000'
>>> hex(25)
'0x19'
>>> hex(1024)
'0x400'
~~~~~

Now Python's integers can become arbitrarily large as they are allowed to have
an arbitrarily large number of binary digits (bits). So in Python we can
easily compute something like $2^{1000}$ like so


~~~~~ python
>>> 2 ** 1000
1071508607186267320948425049060001810561404811
7055336074437503883703510511249361224931983788
1569585812759467291755314682518714528569231404
3598457757469857480393456777482423098542107460
5062371141877954182153046474983581941267398767
5591655439460770629145711964776865421676604298
31652624386837205668069376
~~~~~

However in C it is a bit more complicated than this. In C every elementary
type has a fixed number of bits. The number of bits that computers can use for
anything are always multiples of 8. Really the computer doesn't usually deal
in individual *bits*. Rather it works with whole *bytes* where each byte is a
string of 8 bits.

So let's imagine that we have an 8-bit (1 byte) data type that represents an
integer. The values of this 8-bit data type could be as in this table:

Binary      Hexadecimal        Decimal
--------   -------------   -----------
00000000       0x00           0
00000001       0x01           1
00000010       0x02           2
00000011       0x03           3
...
00011001       0x19           25
...
01111111       0x7F           127
10000000       0x80           128
10000001       0x81           129
...
11111110       0xFE           254
11111111       0xFF           255

Table: The integer values taken by an *8-bit unsigned integer* type shown in
binary, hexadecimal and decimal.

So with an 8-bit data-type we could represent all of the integers from $0$ to
$255$. The number of values we can represent is given by $2^8=256$ and since we
start counting from $0$ the largest representable value is one less than $256$.

The 8-bit data-type described above is known as an *unsigned* type. This is
because it cannot represent negative numbers. To represent negative numbers we
need to use one of the bits to indicate if the number is positive or negative.
The standard way to do this for a *signed* data-type is called 2's complement
and uses the first bit as the *sign bit*. If the first bit is a $0$ then the
value is considered to be positive and the remaining 7 bits give the integer
value. If the first bit is a $1$ then the value is negative and the remaining
7 bits count upwards from the minimum possible value. So a *signed* 8-bit type
would look like:

Binary      Hexadecimal    Decimal
--------   -------------  --------
00000000        0x00             0
00000001        0x01             1
00000010        0x02             2
00000011        0x03             3
...
00011001        0x19            25
...
01111111        0x7F           127
10000000        0x80          -128
10000001        0x81          -127
...
11111110        0xFE            -2
11111111        0xFF            -1

Table: The integer values taken by an *8-bit signed integer* type shown in
binary, hexadecimal and decimal.

Note that the hex value here is the same as in the unsigned table. This is
because I'm treating the hex value as a representation of the bits rather than
the numeric value. To interpret a negative signed binary integer think of the
first 1 as meaning $-128$ and then add to that the positive integer
represented by the remaining 7-bits.

Integers in C
-------------

In C there are a number of different signed and unsigned integer data types
that used fixed numbers of bytes. Usually a type has 1, 2, 4 or 8 bytes (8,
16, 32 or 64 bits). In this particular unit we are only going to consider four
of them `char`, `int`, `unsigned char` and `unsigned int`. Now strictly
speaking the number of bits/bytes used for each of these types depends on the
compiler used and which operating system you are using and other factors. It's
important to know this when working in C but to simplify this discussion I am
going to assume the common case which is that `char`, `unsigned char` are
signed/unsigned 1 byte (8 bit) data types and `int`, `unsigned int` are
signed/unsigned 4 byte (32 bit) data types.

So `unsigned char` is a 1 byte unsigned integer data type. This is exactly
like the unsigned type show in the table above. We can create a variable of
type `unsigned char` simply be using that as the name of type when declaring
the variable. Here is a short program that shows how to use this type:

~~~~~~~ C
/* uchar1.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  unsigned char a = 10;
  unsigned char b = 3;
  unsigned char c = a + b;
  printf("10 + 3 = %u\n", (unsigned int)c);
  return 0;
}
~~~~~~~

[//]: # *]

We can run this to see

~~~~
$ gcc -Wall uchar1.c -o uchar1.exe
$ ./uchar1.exe
10 + 3 = 13
~~~~

Now there's two things to notice in `uchar.c`. Firstly we declare out
variables `unsigned char` to get variables of that type. Secondly in order to
print the value of an unsigned char we use the `%u` format code and we need to
*cast* the variable `c` to `unsigned int` (the type that corresponds to the
`%u` format code). We cannot use `%d` to print an `unsigned char` as sometimes
the wrong value will be printed out if we do.

To see some perhaps less expected behaviour let's make a program that tests
subtraction:

~~~~~~~ C
/* uchar2.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  unsigned char a = 10;
  unsigned char b = 3;
  unsigned char c = b - a;
  printf("3 - 10 = %u\n", (unsigned int)c);
  return 0;
}
~~~~~~~

[//]: # *]

This time we get:

~~~~
$ gcc -Wall uchar2.c -o uchar2.exe
$ ./uchar2.exe
3 - 10 = 249
~~~~

So why does `3 - 10` give us `249`? Firstly it depends on the type of the
variables involved. In this case we are using `unsigned char` which can
represent integers from `0` to `255`.

To understand how the arithmetic works we can think of it in a few different
ways. One way is the idea of *wraparound*. So if we start from `3` and
repeatedly subtract `1` then we will get to `0`. What happens when we subtract
`1` from `0`? It wraps around from `0` the smallest integer to `255` the
largest integer. So to subtract `10` from `3` it goes 3, 2, 1, 0, 255, 254,
253, 252, 251, 250, 249. And there we have it: `249`.

Another way of thinking about the arithmetic is as modular arithmetic. The
result we get is equal to the true result *modulo 256*. What this means is
that we calculate the true result and then find the remainder when dividing it
by 256. Or perhaps take the true result and add or subtract some multiple of
256 until it is within the range 0-255. In this case we have that `10-3` is
$-7$. Since $-7$ is outside of the desired range we can add $256$ to it to get
`249` which is in the range.

Exercises
---------

Test out some more calculations to see if you can understand what happens.
What do you get from `10/3`, `10%3`, `255+1` or `10*100`? What happens if you
write `unsigned char a = -1`?

The signed version of char is just called `char` (on most compilers). Try
using that and testing out arithmetic with negative numbers. See that you can
find the minimum and maximum values. What happens when you divide negative
numbers e.g. `(-10) / 3` or `(-10) % (-3)`? Are the results of these
operations the same as in Python?

In addition to `+`, `-`, `*`, `/` and `%` we have the augmented assignment
operators: `+=`, `-=`, `*=`, `/=`, `%=`, `++` and `--`. Test out how these
work as well.

At this point it is good to make sure that you really understand these results
and can predict them so test out what you need to ensure that you get it.

Ints in C
---------

So an `int` is a signed 4 byte (32 bit) type. The `int` value of 123 looks
like
$$
    123 = 0\mathrm{b}\,\,00000000\,\,00000000\,\,00000000\,\,01111011
$$
Writing out 32-bit numbers in binary is tedious so these are normally written
in hexadecimal. The `int` value of 123 can be written in hexadecimal as
$$
    123 = 0\mathrm{x}\,\,00\,\,00\,\,00\,\,7\mathrm{B}
$$
which is easier to read than the binary form but still shows us the values of
the individual bytes (if you know how to read it).

So how big can an `int` be? Well since we have 32 binary digits we can do
$2^{32} = 4294967296$ possible different values. Since the first bit is the
sign bit there are 31 bits to represent the number and the result is that a C
`int` can take any value greater than or equal to $-2^{31}$ and less than or
equal to $2^{31}-1$. So the minimum and maximum values for an `int` are
$-2147483648$ and $2147483647$ respectively.

Note that when we write an integer literal e.g. `4` or `123` in C code it is
implicitly understood to be an object of type `int` if there are no variables
of any other type in the expression. So we can do calculations with `int` in
a more straight-forward way.

~~~~~~~ C
/* operations.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
   printf("4 * 3 = %d\n", 4 * 3);
   printf("2 + 2 = %d\n", 2 + 2);
   printf("1 - 2 = %d\n", 1 - 2);
   printf("9 / 3 = %d\n", 9 / 3);
   return 0;
}

~~~~~~~

[//]: # *]

We can run this to get:

~~~~~~~~~~
$ gcc -Wall operations.c -o operations.exe
$ ./operations.exe
4 * 3 = 12
2 + 2 = 4
1 - 2 = -1
9 / 3 = 3
~~~~~~~~~~


Overflow
--------

You might think that the maximum values for an `int` is sufficiently large
that we don't need to worry about overflow but it can easily happen:

~~~~~~~ C
/* arithmetic.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
   int a = 1000000; /* one million */
   printf("1 trillion = %d\n", a * a);
   return 0;
}

~~~~~~~

[//]: # *]

If we compile and run this we might see

~~~~~~~~
$ gcc -Wall arithmetic.c -o arithmetic.exe
$ ./arithmetic.exe
1 trillion = -727379968
~~~~~~~~

Now clearly this is mathematically incorrect. Why did we get this number? Well
the correct answer for 1 million squared is 1 trillion but that number is too
big to be represented by an `int` which can have only 4 bytes and has a
maximum value of approximately 2 billion. What happens is that when we do a
calculation whose result exceeds the possible range of an `int` the result
will *overflow* and then we end up getting some random number back. For the
benefit of the curious reader we can calculate why I got this particular value
using Python:

~~~~~~~ Python
>>> n = 1000000*1000000
>>> ((n + 2**31) % 2**32) - 2**31
-727379968
~~~~~~~

[//]: # *]

However in general when a calculation involving a *signed* type such as `int`
overflows it is not possible to predict the actual result that the computer
will produce. Because of this your program may behave differently when run on
different computers so you should avoid ever asking for calculations that
would overflow with signed types (with *unsigned* types the behaviour is
always well defined and is explained above with wraparound or modular
arithmetic).

Exercises
---------

Play with `int` and be sure that you understand how it works.

Similarly try the `unsigned int` type to make sure that you understand how
that works (you'll need to explicitly declare variables of type `unsigned int`
as we did above for `unsigned char`).

Bitwise operations
------------------

Since in C all of our integer data types are represented by bits there are a
number of operations that we can do that will operate on the bits in useful
ways. It is not possible in C to operate on individual bits: a byte is the
smallest unit that we can use. However we have operations that we can use with
bytes that will have particular effects on the bits in each of the bytes.
Specifically we have the operators:

- Bitwise AND `&`
- Bitwise OR `|`
- Bitwise XOR `^`
- Bitwise NOT `~`
- Right shift `>>`
- Left shift `<<`

We also have the augmented assignment form of each of these operators: `&=`,
`|=`, `^=`, `~=`, `<<=`, and `>>=`.

All of these operators should only be used on *unsigned* types. Their effects
are "undefined" for *signed* types (meaning that your program behaves
differently on different computers or with different compilers).

To understand how bitwise AND works we need to understand the logical table
for AND

- `0 AND 0 = 0` (False and False = False)
- `1 AND 0 = 0` (True  and False = False)
- `0 AND 1 = 0` (False and True  = False)
- `1 AND 1 = 1` (True  and True  = True)

So if we have `unsigned char a = 25` and `unsigned char b = 19` then we can
find `a & b` as

              Decimal   Binary
   --------  --------- --------
        `a`   $25$      $00011001$
        `b`   $19$      $00010011$
    `a & b`   $17$      $00010001$

So the first bit of `a & b` is chosen by taking the first bit from `a` and the
first bit from `b` and combining them with the logical AND operation. In this
case since both have `0` for the first bit we get a zero for the first bit of
`a & b`. Since the operation `a & b` does logical AND for each of the bits in
`a` and `b` it is called a *bitwise* operation. We can test it out like so:

~~~~~~~ C
/* bitwise.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  unsigned char a = 25;
  unsigned char b = 19;
  unsigned char c = a & b; /* bitwise and */
  printf("25 & 19 = %u\n", (unsigned int)c);
  return 0;
}
~~~~~~~

[//]: # *]

Running this we get the expected result:

~~~~~~~
$ gcc -Wall bitwise.c -o bitwise.exe
$ ./bitwise.exe
25 & 19 = 17
~~~~~~~

We can easily understand the other bitwise operators in the same way. So
bitwise OR has the following table:

- `0 OR 0 = 0` (False or False = False)
- `1 OR 0 = 1` (True  or False = True)
- `0 OR 1 = 1` (False or True  = True)
- `1 OR 1 = 1` (True  or True  = True)

XOR is short for eXclusive OR. In English language XOR is similar to either/or
as in "either A or B (but not both)". XOr has the table

- `0 XOR 0 = 0` (False xor False = False)
- `1 XOR 0 = 1` (True  xor False = True)
- `0 XOR 1 = 1` (False xor True  = True)
- `1 XOR 1 = 0` (True  xor True  = False)

Finally bitwise NOT operates on a single quantity (unlike the binary operators
above) and simply inverts all the bits so that e.g. $10000100$ would become
$01111011$.

Finally we have the bit-shift operators `<<` and `>>`. These shift the bits to
the left or right by a specified amount. For example we can write `a << 2` to
shift all of the bits in `a` 2 digits to the left. What do I mean by that?
Well suppose that `a` has bits $00001101$ then `a << 2` has bits $00110100$.
Note that in this case this is the same as multiplying `a` by $4$.

When left-shifting any bits that go off the left hand side are thrown away and
the right hand side is padded with zeros. So if `a` has bits $11111111$ then
`a << 3` has bits $11111000$. Right-shifting is the same except that we shift
the bits to the right. Right-shifting by $N$ is equivalent to dividing by
$2^N$ (and rounding down).

Exercises
---------

So what would `25 | 19` or `25 ^ 19` be? How about `~25` or `~19`? Can you see
a relationship between `~17`, `~25` and `~19`? What happens when you bit-shift
these numbers?

Can you work these out on paper and do you get the same when testing it in the
computer?

(The answer you get to these questions depends on whether you are using
`unsigned char` or `unsigned int` so be sure to check both!)

Summary
-------

I now expect you to understand:

- How integers are represented in binary and in hexadecimal.
- How negative integers are handled and signed and unsigned types.
- The four types of integer that we will use in this unit `char`, `unsigned
  char`, `int` and `unsigned int`.
- How the basic arithmetic operators `+`, `-`, `/`, `*` and `%` behave for each of
  these types and how to predict their results.
- The bitwise operators `&`, `|`, `^`, `~`, `<<` and `>>` and how to calculate
  what values they produce.

-------------

Next section: [installing the compiler](installing.html)
