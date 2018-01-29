Quadratic example
=================

The problem
-----------

We will now try to work through the process of making a usable piece of code
with all of the pieces covered up to this point. Our target will be to make a
simple C programme that can calculate the roots of a quadratic polynomial
$$a\,x^2 + b\,x + c$$
where $a\neq 0$. The roots of this polynomial are given by the formula
$$x_{1,2} = \frac{-b \pm \sqrt{b^2-4ac}}{2a}$$

[//]: #_

and we want to make a programme that can calculate these.

First steps
-----------

Now before we begin making any programme we will want to make a folder that
will contain our code and then `cd` into that folder

~~~~
$ mkdir quadratic-1.0
$ cd quadratic-1.0/
$ ls           # Shows nothing as folder is empty
~~~~

The first thing we should always do is write the minimum amount of code that
it takes to get something compiling. It's important that you write code in a
continuous write/compile/test cycle. If you write a lot of code without
compiling it or checking that it works then you'll get into a situation that
is difficult to fix. It's much better to make small changes, test them, and
know that if something has gone wrong it's because the last thing we did
wasn't correct. So we begin by making a hello world program and compiling
that and then we'll change it piece by piece. Here's our hello world:

~~~~ C
/*
 * quadratic.c
 *
 * Author: Oscar Benjamin
 * Date: 30th Jan 2017
 * Description:
 *     This is our first attempt at making
 *     a program that will compute the
 *     roots of quadratic polynomials.
 */

#include <stdio.h>

int main(int argc, char *argv[])
{
    printf("Hello world!\n");
    return 0;
}
~~~~

[//]: #*

Now before complicating the programme any further we will compile it to make
sure that at least this much works correctly:

~~~~
$ gcc -std=c99 -Wall quadratic.c -o quadratic.exe
$ ./quadratic.exe 
Hello world!
~~~~

Okay so far so good. Now the programme doesn't yet do anything useful but we
have a few other things to do before we move on with that. First thing's first
let's make a Makefile so we can use that to compile

~~~~ make
all: quadratic.exe

quadratic.exe: quadratic.c
    gcc -std=c99 -Wall quadratic.c -o quadratic.exe
~~~~

Now we can test that:

~~~~
$ make
make: Nothing to be done for `all'.
$ make -B
gcc -std=c99 -Wall quadratic.c -o quadratic.exe
$ ./quadratic.exe
Hello world!
~~~~

So now that we've created a folder for our code, a Makefile to compile it, and
a fully working compilable programme we're in position to start writing some
more useful code.

Fleshing it out
---------------

The first thing we should probably do is have the program output something
useful for us to look at while developing it. So we'll change the `main`
function to do something more useful.

~~~~ C
int main(int argc, char *argv[])
{
    int a = 1;
    int b = 5;
    int c = 6;

    printf("The roots of %dx^2 + %dx + %d are:\n", a, b, c);

    printf("(-%d + sqrt(%d)) / %d\n", b, b*b-4*a*c, 2*a);
    printf("(-%d - sqrt(%d)) / %d\n", b, b*b-4*a*c, 2*a);

    return 0;
}
~~~~

Now we can test that out

~~~~
$ make
gcc -std=c99 -Wall quadratic.c -o quadratic.exe
$ ./quadratic.exe
The roots of 1x^2 + 5x + 6 are:
(-5 + sqrt(1)) / 2
(-5 - sqrt(1)) / 2
~~~~

Okay once again so far so good. We've carefully chosen these numbers for $a$,
$b$, and $c$ since it gives us roots of $-2$ and $-3$. Now the question is how
to qompute the square root in C. There is a `sqrt` function that is defined
for the `double` (non-integer type that we haven't explained) 
type that we could use but let's just make our own `isqrt` function that works
with type `int`.  While we test our square root function I'll quickly comment
out the other code that prints stuff and only insert some code that prints the
output of the `isqrt` function so now our programme looks like this:

~~~~ C
int isqrt(int);

int main(int argc, char *argv[])
{
    /*  Temporarily comment this part out...
    int a = 1;
    int b = 5;
    int c = 6;

    printf("The roots of %dx^2 + %dx + %d are:\n", a, b, c);

    printf("(-%d + sqrt(%d)) / %d\n", b, b*b-4*a*c, 2*a);
    printf("(-%d - sqrt(%d)) / %d\n", b, b*b-4*a*c, 2*a);
    */

    /* Let's just add this code to see if sqrt works... */
    for(int i=0; i<=10; i++)
    {
        printf("sqrt(%d) = %d\n", i, isqrt(i));
    }

    return 0;
}

/*
 * Fairly dumb algorithm for computing
 * the square root of an integer.
 * Returns the smallest integer x such
 * that x*x >= y. If y is square then this
 * will be it's square root. Otherwise
 * it is the square root rounded up.
 */
int isqrt(int y)
{
    int x = 0;
    while(x*x < y)
    {
        x++;
    }
    return x;
}
~~~~

If we test it then we can see

~~~~
$ make
gcc -std=c99 -Wall quadratic.c -o quadratic.exe
$ ./quadratic.exe
sqrt(0) = 0
sqrt(1) = 1
sqrt(2) = 2
sqrt(3) = 2
sqrt(4) = 2
sqrt(5) = 3
sqrt(6) = 3
sqrt(7) = 3
sqrt(8) = 3
sqrt(9) = 3
sqrt(10) = 4
~~~~

Okay so we get the idea. This function computes the square root when the
number is square or otherwise computes the smallest integer greater than the
square root. You might be wondering why we still return an integer when the
answer isn't an integer. We can represent non-integer numbers in C using the
`float` or `double` types rather than `int`. The problem is that (unlike in
Python) a function always needs to return the same *type* of variable. So we
can't sometimes return `int` and sometimes `double`. Also in this programme I
don't want to compute the result approximately - I always want to print an
exact answer.

So let's use this new `isqrt` function now in our programme. We'll change the
`main` function to

~~~~ C
int main(int argc, char *argv[])
{
    int a = 1;
    int b = 5;
    int c = 6;

    printf("The roots of %dx^2 + %dx + %d are:\n", a, b, c);

    /* Calculate the disciminant and its square root */
    int discriminant = b*b - 4*a*c;
    int sqrtd = isqrt(discriminant);

    /* Check to see if the sqrt is exact */
    if(sqrtd * sqrtd == discriminant)
    {
        printf("%d / %d\n", -b + sqrtd, 2*a);
        printf("%d / %d\n", -b - sqrtd, 2*a);
    }
    /* Otherwise just print it as sqrt(discriminant) */
    else
    {
        printf("(-%d + sqrt(%d)) / %d\n", b, discriminant, 2*a);
        printf("(-%d - sqrt(%d)) / %d\n", b, discriminant, 2*a);
    }

    return 0;
}
~~~~

We can run this now to see

~~~~
$ make
gcc -std=c99 -Wall quadratic.c -o quadratic.exe
$ ./quadratic.exe 
The roots of 1x^2 + 5x + 6 are:
-4 / 2
-6 / 2
~~~~

The correct roots are -2 and -3 so this looks right. Let's try using a
different quadratic now to test what happens if the roots are inexact. If we
change `a`, `b`, and `c` in our programme we can try to find the roots of $x^2
+ x - 1$. This now gives us

~~~~
$ make
gcc -std=c99 -Wall quadratic.c -o quadratic.exe
$ ./quadratic.exe 
The roots of 1x^2 + 1x + -1 are:
(-1 + sqrt(5)) / 2
(-1 - sqrt(5)) / 2
~~~~

So far so good. The problem is that a lot of the output isn't quite the way
that we want it. We should really make a special function for printing
polynomials so that we can make them look really good. We'll do that now.
We'll make a function `print_quadratic` that looks like

~~~~ C
void print_quadratic(int a, int b, int c)
{
    /* For -1 we just use a minus sign */
    if(a == -1)
    {
        printf("-");
    }
    /* Don't print coefficient if it's just 1 */
    else if(a != 1)
    {
        printf("%d", a);
    }
    printf("x^2");
    /* Only print second term if non-zero */
    if(b != 0)
    {
        if(b > 1)
        {
            printf(" + %d", b);
        }
        else if(b < 0)
        {
            // b < 0
            // This ensures a space after - sign
            printf(" - %d", -b);
        }
        else
        {
            // We get here if b == 1
            printf(" + ");
        }
        printf("x");
    }
    /* Only print constant if non-zero */
    if(c != 0)
    {
        if(c > 0)
        {
            printf(" + %d", c);
        }
        else
        {
            printf(" - %d", -c);
        }
    }
}
~~~~

Think about how we normally write a polynomial to understand what's happening
in all the conditions above. For example if the coefficient of $x^2$ is just $1$
then we wouldn't usually include it. Likewise if it $-1$ then we would usually
just write $-x^2$ rather than $-1x^2$. There are other cases non handled by
the code above e.g· what happens if `a` is zero?

In any case let's use the above function in our programme. We put that code at
the bottom of `quadratic.c`. At the top we'll add a declaration for the
function along with the declaration for `isqrt` so it looks like

~~~~ C
int isqrt(int);
void print_quadratic(int a, int b, int c);
~~~~

In our `main` function we'll change the printing code so it looks like

~~~~ C
    /* Show the quadratic we're showing for the user */
    printf("The roots of ");
    print_quadratic(a, b, c);
    printf(" are:\n");
~~~~

Now if we run it then we'll see

~~~~ C
$ make
gcc -std=c99 -Wall quadratic.c -o quadratic.exe
$ ./quadratic.exe
The roots of x^2 + x - 1 are:
(-1 + sqrt(5)) / 2
(-1 - sqrt(5)) / 2
~~~~

Notice how the formatting of the polynomial looks the way that we wanted it
to. As mentioned earlier though it still won't be correct in all cases. The
advantage of having a function though is that if we need to fix that code
there will be one obvious place to fix it. We should at this point try
changing the coefficients to see that it prints out the way that we want to in
all cases of interest.

What bothers me about the `print_quadratic` function though is that it's long
and repetitive. It's good to think when you have long and repetitive code
whether there's a way to simplify it by introducing new functions. Let's do
exactly that:



If we go back to our original coefficients for $x^2 + 5x + 6$ then the
programme prints

~~~~ C
$ make
gcc -std=c99 -Wall quadratic.c -o quadratic.exe
$ ./quadratic.exe 
The roots of x^2 + 5x + 6 are:
-4 / 2
-6 / 2
~~~~

So why didn't I make the code divide those two numbers and print out -2 and -3
above? The answer is that the division may not be exact since we are working
with the `int` type. Before dividing them we would need to check that the
numerator is divisible by the denominator. Otherwise it's best to print them
out in rational form. So let's fix that now by changing the code that handles
exact square roots to

~~~~ C
        print_rational(-b + sqrtd, 2*a);
        printf("\n");
        print_rational(-b - sqrtd, 2*a);
        printf("\n");
~~~~

where the `print_rational` function is given by

~~~~ C
void print_rational(int num, int denom)
{
    if(num % denom == 0)
    {
        printf("%d", num / denom);
    }
    else
    {
        printf("%d/%d", num, denom);
    }
}
~~~~

Now when we run this we get

~~~~
$ make
gcc -std=c99 -Wall quadratic.c -o quadratic.exe
$ ./quadratic.exe 
The roots of x^2 + 5x + 6 are:
-2
-3
~~~~

And this is exactly what we want in this particular case. There are still
many ways that this code can be improved but for now this will do. So the
complete programme at this point looks like

~~~~ C
/*
 * quadratic.c
 *
 * Author: Oscar Benjamin
 * Date: 30th Jan 2017
 * Description:
 *     This is our first attempt at making
 *     a program that will compute the
 *     roots of quadratic polynomials.
 */

#include <stdio.h>

int isqrt(int);
void print_rational(int num, int denom);
void print_quadratic(int a, int b, int c);

int main(int argc, char *argv[])
{
    int a = 1;
    int b = 5;
    int c = 6;

    /* Show the quadratic we're showing for the user */
    printf("The roots of ");
    print_quadratic(a, b, c);
    printf(" are:\n");

    /* Calculate the disciminant and its square root */
    int discriminant = b*b - 4*a*c;
    int sqrtd = isqrt(discriminant);

    /* Check to see if the sqrt is exact */
    if(sqrtd * sqrtd == discriminant)
    {
        print_rational(-b + sqrtd, 2*a);
        printf("\n");
        print_rational(-b - sqrtd, 2*a);
        printf("\n");
    }
    /* Otherwise just print it as sqrt(discriminant) */
    else
    {
        printf("(-%d + sqrt(%d)) / %d\n", b, discriminant, 2*a);
        printf("(-%d - sqrt(%d)) / %d\n", b, discriminant, 2*a);
    }

    return 0;
}

/*
 * Fairly dumb algorithm for computing
 * the square root of an integer.
 * Returns the smallest integer x such
 * that x*x >= y. If y is square then this
 * will be it's square root. Otherwise
 * it is the square root rounded up.
 */
int isqrt(int y)
{
    int x = 0;
    while(x*x < y)
    {
        x++;
    }
    return x;
}

void print_rational(int num, int denom)
{
    if(num % denom == 0)
    {
        printf("%d", num / denom);
    }
    else
    {
        printf("%d/%d", num, denom);
    }
}

void print_quadratic(int a, int b, int c)
{
    /* For -1 we just use a minus sign */
    if(a == -1)
    {
        printf("-");
    }
    /* Don't print coefficient if it's just 1 */
    else if(a != 1)
    {
        printf("%d", a);
    }
    printf("x^2");
    /* Only print second term if non-zero */
    if(b != 0)
    {
        if(b > 1)
        {
            printf(" + %d", b);
        }
        else if(b < 0)
        {
            // b < 0
            // This ensures a space after - sign
            printf(" - %d", -b);
        }
        else
        {
            // We get here if b == 1
            printf(" + ");
        }
        printf("x");
    }
    /* Only print constant if non-zero */
    if(c != 0)
    {
        if(c > 0)
        {
            printf(" + %d", c);
        }
        else
        {
            printf(" - %d", -c);
        }
    }
}
~~~~

Download the code
-----------------

[Quadratic 1.0](examples/quadratic-1.0.zip)

------------
Next section: [Exercises](exercises2.html)
