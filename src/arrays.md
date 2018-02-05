Arrays in C
===========

Arrays in C are the basic way to collect objects together in a single
variable. Python has a wealth of of *container* types: `list`, `tuple`, `dict`
etc. In C we only have two types of collection: *arrays* and *structs*.
Structs are for grouping together a fixed number of objects of possibly
different types. Arrays are for grouping together a variable number of objects
of the same type.

A simple array program
----------------------

Let's look at a simple program that uses an array:

``` C
/* array.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  int primes[10] = {2, 3, 5, 7, 11,
                    13, 17, 19, 23, 29};

  for(int i=0; i<10; i++)
  {
    printf("%d is prime\n", primes[i]);
  }

  return 0;
}
```

When we run this we get

```
$ make
gcc -std=c99 -Wall array.c -o array.exe
$ ./array.exe
2 is prime
3 is prime
5 is prime
7 is prime
11 is prime
13 is prime
17 is prime
19 is prime
23 is prime
29 is prime
```

So let's break down the program above. Firstly we have a variable called
`primes` which is declared as `int primes[10]`. This means that it is an array
of 10 `int`s. Since each `int` needs 4 bytes the array needs 40 bytes in
total. The compiler will allocate a contiguous block of 40 bytes in memory for
this array. When I say that the block is *continguous* I mean that the 40
bytes are all "next to" each other. All bytes in memory are numbered with
non-negative integers. This block of bytes would go from e.g. byte number 256
through to byte number 295.

So we've looked at the array *declaration*. We can *initialise* an array using
an expression with curly brackets and items separated by commas e.g.
`{2, 3, 5, ...}`. This means that within the block of 40 bytes the first 4
bytes will store the number 2 (in signed 4-byte integer format). The second 4
bytes will store the number 3 etc.

We can access the elements of the array using subscript notation (square
bracketes) just as we would for a list in Python. So to get the 3rd element of
the array we would use `primes[2]`. Note that just like in Python array
indices count from zero so the 1st element is `primes[0]` and if the array has
length 10 then the last element is `primes[9]`.

Less simple array program
-------------------------

Now let's look at a slightly more advanced program:

``` C
/* array2.c */

#include <stdio.h>

void print_array(int numbers[5]);

int main(int argc, char *argv[])
{
  int primes[5] = {2, 3, 5, 7, 11};

  printf("Before: ");
  print_array(primes);

  printf("Setting primes[3] to -1\n");
  primes[3] = -1;

  printf("After: ");
  print_array(primes);

  return 0;
}

/* Print as C syntax array */
void print_array(int numbers[5])
{
  printf("{%d", numbers[0]);
  for(int i=1; i<5; i++)
  {
    printf(", %d", numbers[i]);
  }
  printf("}\n");
}
```

And running this gives

```
$ make
gcc -std=c99 -Wall array2.c -o array2.exe
$ ./array2.exe
Before: {2, 3, 5, 7, 11}
Setting primes[3] to -1
After: {2, 3, 5, -1, 11}
```

This last program brings up a few new things to notice. Firstly in order to
print an array we actually need to make our own `print_array` function. Unlike
in Python there is no special support for printing anything other than
elementary types in C. So we've defined a function that takes an array `int
numbers[5]`. We can declare the argument to the function to be an array of a
particular size e.g. 5 in this case. The function that we have used is
declared as having `void` return type which is to say that it does not return
anything. Also we can modify the 4th element of the array with the statement
`primes[3] = -1` which sets that element to `-1`.

One thing to note here is that although we've defined the array to be of type
`int[5]` the size of the array is ignored by the compiler when we pass the
array into a function. So if you change the size of the array to 4 in the
above e.g. `int primes[4] = {2, 3, 5, 7}` then you get:

```
$ make array2.exe
gcc -std=c99 -Wall array2.c -o array2.exe
$ ./array2.exe
Before: {2, 3, 5, 7, 0}
Setting primes[3] to -1
After: {2, 3, 5, -1, 0}
```

So the compiler didn't warn us that we were passing an array of type `int[4]`
into a function that expects an array of type `int[5]`. Also if primes is an
array of size 4 then how has the `print_array` function printed a 5th element?
The answer is that it has simply printed whatever is in the computer's memory
*after* the block which is reserved for the array. It's just chance that we
happen to get zero in this case. So how do we deal with this?

The problem is that in C an array really is just a block of memory. When we
pass the array into the function really all that happens is that the number
representing the start of that block of memory is passed into the function.
Within the function `numbers` refers to the same block of memory as `primes`.
When we ask for e.g. `numbers[3]` we get the `int` value of the 4 bytes
beginning 12 ($=3\times 4$) bytes from the start of that block.

So in C when we pass an array into a function there's no way for the code
inside the function to know how big the array is. The solution is to pass in
the size of the array as a separate input to the function.

``` C
/* array3.c */

#include <stdio.h>

void print_array(int numbers[], int size);

int main(int argc, char *argv[])
{
  int primes[] = {2, 3, 5, 7, 11,
                  13, 17, 19, 23, 29};

  print_array(primes, 10);

  return 0;
}

/* Print as C syntax array */
void print_array(int numbers[], int size)
{
  printf("{");
  for(int i=0; i<size; i++)
  {
    if(i)
    {
      printf(", ");
    }
    printf("%d", numbers[i]);
  }
  printf("}\n");
}
```

Again there's a few things to notice here. One is that we can declare an array
to have type `int[]` without specifying the size. This only works for an array
declaration which also initialises the array: the compiler will calculate the
size by looking at the value used to initialise the array (a sequence of 10
numbers in this case).  Another thing to notice is that since the size of the
array is irrelevant when passing it into a function we can declare the type of
the argument to `print_array` to be of type `int[]`.

We now have a `print_array` function that can work for an array of any size.
However we will always need to remember to pass in the correct size when
calling the function. This is generally what happens when working with arrays
in C.

Exercise - reverse an array
---------------------------

Write a function that can reverse the elements of an array. In Python this is
easily done with e.g. `reversed(mylist)` or `mylist[::-1]` but in C this is a
little less easy. The main problem is that we don't have any way to create an
array inside a function and then *return* that to the caller. A common
solution in C is to modify the array that is passed in as an argument to a
function. This is known as modifying the input array *in-place*. Write a
function called `reverse_inplace` that can be used in this way e.g.

``` C
  int numbers[10] = {1, 2, 3, 4, 5,
                     6, 7, 8, 9, 10};

  // Prints out {1, 2, ...}
  print_array(numbers, 10);

  // This is the function you need to write:
  reverse_inplace(numbers, 10);

  // Prints out {10, 9, ...}
  print_array(numbers, 10);
```

Hint: Reversing the array swaps opposite elements, so e.g. the first element
swaps with the last, the second first with the second last etc.

Exercise - sieve of Eratosphenes
--------------------------------

The sieve of Eratosphenes is a way of efficiently computing all of the prime
numbers less than some maximum number. For example suppose that we want to
find all prime numbers less than 30. We begin by writing out a table of all of
the numbers from 1 to 30:

```
01 02 03 04 05 06 07 08 09 10
11 12 13 14 15 16 17 18 19 20
21 22 23 24 25 26 27 28 29 30
```

Now 1 is not prime so we cross that out (we'll set it to -)

```
-- 02 03 04 05 06 07 08 09 10
11 12 13 14 15 16 17 18 19 20
21 22 23 24 25 26 27 28 29 30
```

The first non-blank character is 2 so now we cross out all multiples of 2
except 2 itself:

```
-- 02 03 -- 05 -- 07 -- 09 --
11 -- 13 -- 15 -- 17 -- 19 --
21 -- 23 -- 25 -- 27 -- 29 --
```

The trick is that removing all multiples of 2 just means looping through the
array in steps of 2 elements at a time. Moving on from 2 we find that the next
non-blank element in the array is 3. This means that 3 is prime and now we
will remove all multiples of 3:

```
-- 02 03 -- 05 -- 07 -- -- --
11 -- 13 -- -- -- 17 -- 19 --
-- -- 23 -- 25 -- -- -- 29 --
```

Next prime number is 5 so remove all multiples of 5:

```
-- 02 03 -- 05 -- 07 -- -- --
11 -- 13 -- -- -- 17 -- 19 --
-- -- 23 -- -- -- -- -- 29 --
```

Now $6^2$ is bigger than $30$ which means that the sieve is complete. We have
now filtered out all of the prime numbers from the set of all numbers from 1
to 30.

Your exercise is to make a program that can use this method to find all the
prime numbers from e.g. 1 to 100.

------------
Next section: [Strings](strings.html)
