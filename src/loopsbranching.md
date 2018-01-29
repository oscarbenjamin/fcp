Loops and branching
-------------------

Having spent a little time talking about using the shell and make files we're
now back to plain old C code. The topic here is loops and branching which are
the two basic types of control flow in *imperative programming*.

If statements
-------------

Just like in Python in C we have `if` statements that can define when to
conditionally execute different blocks of code e.g.:

~~~~ C
  if(x > 5)
  {
    printf("x is greater than 5");
  }
~~~~

The main differences from Python here are that the condition attached the `if`
statement (`x > 5`) needs to be in brackets and that the body of code which is
executed is enclosed in curly brackets `{` and `}`. In Python the block of
code is defined by the indentation level. In C the indentation is optional but
is stylistically preferred. (For the purposes of this unit correct indentation
is *not* optional.)

If the block of code to execute is only one statement then we can shorten this to

~~~~ C
  if(x > 5)
    printf("x is greater than 5\n");
~~~~

However this form is often discouraged since it can lead to bugs like

~~~~ C
  if(x > 5)
    printf("x is greater than 5\n");
    printf("and x is greater than 4\n");
~~~~

In this last example only the first `printf` statement is part of the
if-block. The second looks as if it is because it's indented but the rule is
that the block of code is either enclosed in curly brackets or consists of
only one statement. The same problem occurs if we put multiple statements on
one line:

~~~~ C
  if(x > 5)
    printf("x > 5\n"); printf("x > 4\n");
~~~~

Only the first `printf` statement is considered to be in the body of the if
construct. The indentation and the line spacing are irrelevant.

Because of this I prefer the long form with the curly brackets and I expect
you to use this in your code.

Else
----

We can also attach `else` and `else if` clauses to an `if` statement e.g.:

~~~~ C
  if(x > 5)
  {
    printf("x is greater than 5\n");
  }
  else if(x < 0)
  {
    printf("x is negative\n");
  }
  else
  {
    printf("0 <= x <= 5\n");
  }
~~~~

Hopefully you can work out what that does already. Give it a try in a simple
program.

Relations and logical operators
-------------------------------

C defines the same binary relations as Python. Specifically `<`, `<=`, `==`,
`>=`, `>`, `!=`. Hopefully you've already seen these. What is different
between Python and C is that in Python an expression like `a < b` results in
an object of type `bool` having the value `True` or `False`. In C logical
expressions have type `int` and result in the values `1` or `0`. So for
example these two are equivalent:

~~~~ C
  if(a == 0)
  {
    // do stuff
  }
  if(a)
  {
    // do stuff
  }
~~~~

When we use a condition as part of an `if` statement the condition is
considered true if it is non-zero and false if it is zero.

The logical operators in C are `&&`, `||` and `!` which are analogous to the
Python operators `and`, `or` and `not`. We can use these to build a more
complex condition e.g.:

~~~~ C
  if((0 <= a) && (a < N))
  {
    printf("0 <= a < N\n");
  }
~~~~

In Python we could simply write that as

~~~~ python
  if 0 <= a < N:
    print('0 <= a < N')
~~~~

However C does not allow "relation chaining" in this way.

Another point to be aware of which is similar to Python is short-circuiting of
logical operators. In an expression `a && b` first the `a` part of the
expression will be evaluated. If it is false (`0`) then there is no need to
evaluate the `b` part of the expression since the expression is already known
to have the value `0`. For example in the following code the function `f()` is
never called:

~~~~ C
  if((3 < 2) && f())
  {
    // do stuff
  }
~~~~

The expression `3 < 2` is false so evaluates to `0`. The expression `0 &&
f()` will always be false regardless of the value of `f()` so the there is no
need to call `f`. Short-circuiting happens left-to-right so the left most
expression is always evaluated first and then the expressions to the right are
only evaluated if necessary. Short circuiting also happens with `||` so that
in the expression `a || b` the expression `b` is only evaluated if the
expression `a` evalutes to `0`.

While loops
-----------

Again `while` loops in C are similar to Python:

~~~~ C
  int i=0;
  while(i*i < 10)
  {
    printf("i = %d\n", i);
    i++;
  }
  printf("%d^2 >= 10\n", i);
~~~~

[//]: # *]

The output from the above is

~~~~ C
i = 0
i = 1
i = 2
i = 3
4^2 >= 10
~~~~

Again the differences are mostly superficial. In C the condition attached to a
`while` loop must be enclosed in brackets and the body of the loop is enclosed
in curly brackets. Otherwise it works the same as in Python. We can use all of
the same logical relations and operators in a `while` loop condition as we can
in an `if` statement.

Pulling the above loop apart what happens is that execution begins with
setting `i` to `0`. We then enter the loop. The condition is checked at the
*start* of each loop iteration. Since `i` is initially `0` the condition `i*i < 10`
becomes `0 < 10` which is true so evaluates to `1`. This means that the
loop body will execute. The loop body has the statement `i++` which increases
`i` from `0` to `1`. Execution then goes back to the top of the while loop and
the condition is checked again, the body is executed again and so on until `i`
becomes `4`. At that point `4*4 < 16` is false so evaluates to `0` terminating
the loop. Execution then resumes after the loop at the `printf` statement
which prints `4^2 >= 10` (4^2 is supposed to be read as "4 squared" although
the caret operator `^` actually has a different meaning in C). Note that the
line `i = 4` is not printed.

For loops
---------

For loops in C are different to those in Python. In Python we loop over some
*object* e.g. a `list` or a `range`. In C a `for` loop is just an alternate
syntax for common forms of `while` loop. A `for` loop in C looks like:

~~~~ C
/* forloop.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  for(int i=0; i<5; i++)
  {
    printf("i = %d\n", i);
  }
  printf("finished\n");
  return 0;
}
~~~~

[//]: # *]

We can run this to see

~~~~
$ make
gcc -std=c99 -Wall forloop.c -o forloop.exe
$ ./forloop.exe
i = 0
i = 1
i = 2
i = 3
i = 4
finished
~~~~

The syntax for a for loop is

~~~~ C
  for(<initialiser>; <condition>; <iterate>)
  {
    <body>
  }
~~~~

The execution of a for loop begins by executing the `<initialiser>` statement.
Then at the start of each iteration the `<condition>` expression is checked
(just like for a `while` loop). Then the loop `<body>` is executed and after
that the `<iterate>` statement is executed. At that point execution goes back
to the top of the loop and `<condition>` is checked again. We can rewrite this
general form as a while loop like so

~~~~ C
  <initialiser>
  while(<condition>)
  {
    <body>
    <iterate>
  }
~~~~

Any of the three statements in the `for()` line is optional. We can make an
infinite loop simply by omitting all three:

~~~~ C
  for(;;)
  {
    // infinite loop
  }
~~~~

Although I've just discussed `for` loops in their full generality, by far the
most common type of for loop simply iterates over some integers from `0` to `N-1`. This is equivalent to looping over a `range` in Python e.g.:

~~~~ python
  for n in range(N):
    # do stuff with n
~~~~

The equivalent in C is just

~~~~ C
  for(int n=0; n<N; n++)
  {
    /* do stuff with n */
  }
~~~~

[//]: # >

Note that this loop takes `n` through the values `0`, `1`, `2`, ..., `N-1`. It
does not include an iteration with `n=N`. This is the same as Python's range
object. If we do want a loop that goes from `0` to `N` *inclusive* then we can
use `<=` instead of `<` in the condition.

~~~~ C
  for(int n=0; n<=N; n++)
  {
    /* do stuff with n */
  }
~~~~

------------
Next section: [Functions](functions.html)
