Exercises
=========

Exercise - reverse an array
---------------------------

Write a function that can reverse the elements of an array. In Python this is
easily done with e.g. `reversed(mylist)` or `mylist[::-1]` but in C this is a
little less easy. The main problem is that we don't have any way to create an
array inside a function and then *return* that to the caller. A common
solution in C is to modify the array that is passed in as an argument to a
function. This is known as modifying the input array *in-place*. Write a
function called `reverse_inplace` that can be used in this way e.g.

``` c
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


Exercise - numbers on the command line
--------------------------------------

Extend the cubic integer root-finder program from [last
week](exercises2.html#cubic-solver). Your program should now take its input
from the command line e.g.:

```
$ ./findroots.exe 1 -4 -7 10
Roots of x^3 - 4x^2 - 7x + 10:
-2 1 5
```

Bonus points: can you make this work for a polynomial of any order?
