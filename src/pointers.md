Pointers
========

Pointers are a fundamental concept in C and are one of the things that don't
appear in higher level languages. They are also difficult to get right and a
common source of frustration for new programmers.

So what is a pointer? The memory (RAM) in your computer consists of a number
of byte slots each of which can store one byte. My computer has 8GiB of
memory which means that the number of byte slots is $8\times 1024\times
1024\times 1024 = 8589934592$. Each byte slot has an integer number starting
from 0.

Suppose we have a variable `x` of type `int` which needs 4 bytes. If
the variable is stored at memory location 256 then it uses the byte slots
numbered 256, 257, 258, and 259. We say that the *address* of `x` is 256. The
terms "memory location" and "address" are equivalent and simply refer to the
integer number of the first byte slot used to store a variable.

A pointer in C is an integer variable that stores the address of another
variable. We say that the pointer "points to" the variable whose address is
stores. If a pointer `px` points to a variable `x` then we can use `px` as a
way to access `x` indirectly. This means that we can read or modify the value
of the variable `x` through `px`. Since `px` itself is a variable we can
change its value by making it point to another variable. We do this by storing
the address of a different variable in `px`.

Here's an example from the slides:

``` c
/* pointers.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  int a = 1;
  int b = 2;
  int *pointer;
  printf("1: a = %d   b = %d\n", a, b);
  pointer = &a;
  printf("2: *pointer = %d\n", *pointer);
  a = 3;
  printf("3: *pointer = %d\n", *pointer);
  *pointer = 123;
  printf("4: a = %d   b = %d\n", a, b);
  pointer = &b;
  printf("5: *pointer = %d\n", *pointer);
  *pointer = -1;
  printf("6: a = %d   b = %d\n", a, b);
  return 0;
}
```

And here's the output:

```
$ ./pointers.exe
1: a = 1   b = 2
2: *pointer = 1
3: *pointer = 3
4: a = 123   b = 2
5: *pointer = 2
6: a = 123   b = -1
```

So let's try to pull this apart. Firstly the statement `int *pointer` declares
a variable with name `pointer` and type `int*`. The type `int*` is read as
"pointer to int". It means that the `pointer` is a pointer type variable but
the compiler should interpret the variable it points to as being of type `int`.

The line `pointer = &a` initialises the pointer with the memory address of the
variable `a` which is of type `int`. If `a` was of a different type e.g. `char`
then the compiler would complain since `pointer` has type `int*` an should
point to a variable of type `int`. The operator `&` used in this way is called
the *address of* operator. We can use it on any variable and gives us the
integer number of the first byte slot used by the variable. So this line
stores that address in `pointer`.

The line `printf("2: *pointer = %d\n", *pointer);` uses `*pointer` as the
value to print out. When we have a pointer variable - such as `pointer` in this
example - writing `*pointer` means the value of the variable pointed to by
pointer. The operator `*` used in this way is called the *dereferencing*
operator. In a sense the dereferencing operator is the inverse of the address
of operator since for any variable `a` we would have that `a==*(&(a))`.
Writing the operators the other way round (`&(*(a))`) doesn't work unless `a`
has a pointer type (non-pointers cannot be dereferenced). Note how the
expression `&a` implicitly means a value of type pointer to `int` if `a` has
type `int`. Likewise if `a` has type `int*` then `*a` has type `int`.

We can use the dereferencing operator to read a variable as just described or we
can use it to modify a value. The statement `*pointer = 123` does not change
the value of `pointer`. Rather it changes the value of the variable pointed at
by `pointer`. So we can use `*pointer` anywhere in a statement that we would
use a normal variable name but the effect is to read/write the value of the
variable whose address `pointer` stores rather than changing `pointer` itself.
This is different from the statement `pointer = &b` which causes the pointer
to point at a different object and does not modify the value of the variables
pointed to by the pointer.

Exercise
--------

Study the above program carefully and ensure that you understand each line of
output. Make your own program that uses pointers to modify variables and print
out the value of all of the variables. Play around with this until you're sure
that you understand this concept.

Why pointers?
-------------

Okay so we've talked about what pointers are now the question is: why would we
want to use them? Let's work through a few simple programmes to see.

Firstly in C we can only ever return one value from a function. So let's say I
want to have a function that parses a decimal string (e.g. "123") and returns
the corresponding integer value. Let's quickly make a function like that and a
short programme to go with it:

``` c
/* double.c */

#include <stdio.h>
#include <ctype.h>

int int_from_decimal_string(char decimal_string[]);

int main(int argc, char *argv[])
{
    if(argc == 2)
    {
        int number = int_from_decimal_string(argv[1]);
        printf("2*%d = %d\n", number, 2*number);
        return 0;
    }
    else
    {
        fprintf(stderr, "Usage:\n./double.exe NUM\n");
        return 1;
    }
}

int int_from_decimal_string(char decimal_string[])
{
    /* Check that the string is all digits */
    for(int i=0; decimal_string[i] != '\0'; i++)
    {
        if(!isdigit(decimal_string[i]))
        {
            fprintf(stderr,
                    "Bad decimal string \"%s\"\n",
                    decimal_string);
            return -1; // What should we return?
        }
    }

    int total = 0;
    for(int i=0; decimal_string[i] != '\0'; i++)
    {
        total = total * 10 + (decimal_string[i] - '0');
    }

    // Success
    return total;
}

```

We can run this programme like so

```
$ make
gcc -std=c99 -Wall double.c -o double.exe
$ ./double.exe 123
2*123 = 246
$ ./double.exe 12
2*12 = 24
```

Okay so far so good. Now what happens if we give the programme an invalid
string? Let's try it out:

```
$ ./double.exe asd
Bad decimal string "asd"
2*-1 = -2
$ ./double.exe -1
Bad decimal string "-1"
2*-1 = -2
```

So the function `int_from_decimal_string` has a problem. When it finds an
invalid decimal string it can print a message to `stderr` but it still needs
to return a value. In this case it returns `-1`. So how can the calling code
check that an error occurred? Well the calling code can just check for a
return value of `-1`.

But what if `-1` is a valid return value from the function? The function
doesn't check for minus signs but we could try and improve it to do so and
then what integer value could we possibly return to indicate that an error
occurred?

If we were working in Python we could raise an exception which would terminate
the calling code. However in C there are no exceptions so any error has to be
signalled somehow through the values a function returns. So maybe the function
could return two values? One value would be the integer that results from
parsing the string and the other could be a flag that indicates whether the
function succeeded or not.

So how do we return two values from a function? There are two basic answers to
this question. One is that we can return a struct. The other is that we can
modify the inputs to the function using pointers. In this article we'll focus
on using pointers.

Modifying input arguments
-------------------------

So how do we modify the input arguments to a function? Well let's just try it
and see what happens:

``` c
/* inputs.c */

#include <stdio.h>

void modify_value(int a);

int main(int argc, char *argtv[])
{
    int a = 2;
    modify_value(a);
    printf("main: a = %d\n", a);
    return 0;
}

void modify_value(int a)
{
    a = 1;
    printf("modify_value: a = %d\n", a);
}
```

Okay so let's run that and see what happens:

```
$ make
gcc -std=c99 -Wall inputs.c -o inputs.exe
$ ./inputs.exe 
modify_value: a = 1
main: a = 2
```

Is that what you expected? We can see that `a` was set to 1 in `modify_value`
but then it still printed out as 2 in `main`.  Why is that? Really I'm
deliberately confusing you by calling the parameter to `modify_value` the same
name `a` as the variable in `main`. The programme above is roughly equivalent
to

``` c
/* inputs.c */

#include <stdio.h>

void modify_value(int b);

int main(int argc, char *argv[])
{
    int a = 2;
    modify_value(a);
    printf("main: a = %d\n", a);
    return 0;
}

void modify_value(int b)
{
    b = 1;
    printf("modify_value: b = %d\n", b);
}
```

What we can see more clearly in this example is that the variable `b` in
`modify_value` is a different variable than the `a` in `main` (it doesn't
matter if they have the same name or not). So what happens when we call
`modify_value`? The `modify_value` function receives a *copy* of the *value*
of the `a` variable. The value is copied to a new variable which is called
`b` in `modify_value`. Inside the `modify_value` function we can modify that
copy but it doesn't affect the variable `a` in the `main` function.

So how do we make a function that modifies one of its inputs if the function
always receives copies of its input arguments? We can send the function a
pointer. This works because a copy of a pointer still points to the same
memory location. So how does that look:

``` c
/* inputs.c */

#include <stdio.h>

void modify_value(int *pb);

int main(int argc, char *argv[])
{
    int a = 2;
    modify_value(&a);
    printf("main: a = %d\n", a);
    return 0;
}

void modify_value(int *pb)
{
    *pb = 1;
    printf("modify_value: *pb = %d\n", *pb);
}
```

And when we run this we see

```
$ ./inputs.exe 
modify_value: *pb = 1
main: a = 1
```

So there's a few points to note. Firstly we declare the argument is being of
type pointer to int (i.e. `int*`). Secondly within the function we modify not
`pb` but the value pointed to by `pb` using `*pb`. It is conventional that
pointer variables often have a name that begins with 'p'. Also when we call
`modify_value` we don't pass in `a` but rather `&a` - the address of `a`. The
address of `a` is *copied* to `pb` when the function is called. However the
copy of the memory location still points to the same location so when we
modify what it points to we are modifying the value of the variable `a` in the
`main` function.

`sscanf` revisited
------------------

Now generally modifying the input arguments to a function is considered bad
practice. However there are many places in C where it is the natural way to do
things and there are some particular functions that need to work this way. A
good example is the `sscanf` function that we have already seen. A simple
example of use is

``` c
/* double2.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
    if(argc == 2)
    {
        int number;
        int ret;

        ret = sscanf(argv[1], "%d", &number);
        if(!ret)
        {
            fprintf(stderr,
                    "Invalid decimal string \"%s\"\n",
                    argv[1]);
            return 1;
        }

        printf("2 * %d = %d\n", number, 2*number);
        return 0;
    }
    else
    {
        fprintf(stderr, "Usage:\n./double.exe NUM\n");
        return 1;
    }
}
```

And when we run this we see

```
$ ./double2.exe asd
Invalid decimal string "asd"
$ ./double2.exe 123
2 * 123 = 246
```

We're now in a position to understand what goes in with `sscanf`. Notice that
we get two different values from `sscanf`. We have the value `ret` which is
returned and also it sets the value of the variable `number` which is provided
as input. We can now see why we actually pass the *memory address* of `number`
(i.e. `&number`) - with that `sscanf` can modify the value of `number`. Since
`ret` is returned separately from `number` we have a way to detect if an error
occurred in `sscanf` without needing to assume that any particular value of
`number` represents an error.


Pointers and arrays
-------------------

We are now in a position to understand how array access syntac works. An array
in C is two things: it is a block of memory and it is a variable that refers
to that block of memory. We can use the variable name to access the elements
of the array using subscript notation. Really this just uses pointer
arithmetic. Consider the following:

``` c
/* pointerarray.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  int array[] = {4, 3, 10, 5};
  int *pointer = &array[0];

  printf("array[2]       = %d\n", array[2]);
  printf("*pointer       = %d\n", *pointer);
  printf("*(pointer + 2) = %d\n", *(pointer + 2));
  printf("pointer[2]     = %d\n", pointer[2]);

  return 0;
}
```

When we run this we see

```
$ ./pointerarray.exe
array[2]       = 10
*pointer       = 4
*(pointer + 2) = 10
pointer[2]     = 10
```

This shows us a few things. Firstly we can set a pointer to piont to the first
element of an array and then we can use the `pointer` variable to access the
array with subscript notation just as we could with `array`. The line
`int *pointer = &array[0]` sets `pointer` to point to the first element of
`array`. Afterwards both `pointer[2]` and `array[2]` refer to the same element
of the array.

The third line printed shows us that `pointer[2]` is equivalent to
`*(pointer + 2)`. In other words if `pointer` is a pointer to a memory address
then `pointer + 2` points to the memory address that is two steps along in
memory. In the case of our array if `pointer` points to the first element then
the memory address two steps along is the third element.

In the last paragraph I've referred to the memory address that is two "steps"
along rather than two *bytes* along. The reason for this is that each `int`
uses 4 bytes. So actually if we think of the pointer is the integer number for
a byte in memory then `pointer + 2` will be the integer number for the byte
that is 8 bytes further along. The compiler knows that our pointer is a
pointer to `int` and therefore knows that when we say `pointer + 1` we want to
get the memory address that is 4 bytes further on from the location currently
referred to by `pointer`. We can see this directly by printing out the value
of the pointer in hex using the "%p" format code to `printf`. This prints out
something like the integer number of the memory address for a pointer:


``` c
/* pointerarithmetic.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  int x;
  int *px = &x;

  printf("sizeof(int) = %d\n", (int)sizeof(int));
  printf("px   = %p\n", px);
  printf("px+1 = %p\n", px + 1);

  return 0;
}
```

When we run this we get:

```
$ ./pointerarithmetic.exe
sizeof(int) = 4
px   = 0x7fffa4d2917c
px+1 = 0x7fffa4d29180
```

The two pointer values are printed in hexadecimal. Just looking at the end of
the two pointer values we can see that only the last two digits are different:
`px` ends with $7c$ whereas `px+1` ends with $80$. The difference between
these two is 4 which is the same as the size of an `int`.

------------
Next section: [structs](structs.html)
