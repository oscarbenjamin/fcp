Quadratic 2.0
=============

Let's return to our quadratic solving programme now. If you haven't read the
previous part then you can find that [here](quadratic1.html).

Armed with our new ability to handle command line arguments, process strings
and use arrays let's return to the quadratic programme and make it take its
input coefficients on the command line.

What we had before
------------------

Our `main` function previously began with something like this:

``` C
int main(int argc, char *argv[])
{
    int a = 1;
    int b = 5;
    int c = 6;

    /* Show the quadratic we're showing for the user */
    printf("The roots of ");
    print_quadratic(a, b, c);
    printf(" are:\n");
```

The problem with this is that every time we want to change the coefficients
(`a`, `b`, and `c`) we have to recompile the programme. This is because the
values of the coefficients are *hard-coded* which means that we put them
directly in our code.

How can we solve this? We can make our programme take these numbers from the
command line. Then someone can rerun our programme again and again typing
different arguments in the terminal without having to edit the code and
recompile it each time. So how do we change our programme to do that?

We can change the first part of our `main` function to look like

``` C
int main(int argc, char *argv[])
{
    /* The three coefficients */
    int a, b, c;

    /* Parse command line arguments */
    if(argc != 4)
    {
        fprintf(stderr,
                "Usage:\n./quadratic.exe A B C\n");
        return 1;
    }
    else if(!sscanf(argv[1], "%d", &a))
    {
        fprintf(stderr,
                "Invalid coefficient \"%s\"\n",
                argv[1]);
        return 1;
    }
    else if(!sscanf(argv[2], "%d", &b))
    {
        fprintf(stderr,
                "Invalid coefficient \"%s\"\n",
                argv[2]);
        return 1;
    }
    else if(!sscanf(argv[3], "%d", &c))
    {
        fprintf(stderr,
                "Invalid coefficient \"%s\"\n",
                argv[3]);
        return 1;
    }

    /* Show the quadratic we're solving */
    printf("The roots of ");
    print_quadratic(a, b, c);
    printf(" are:\n");
```

Now if we run it we can see that it checks for bad input:

```
$ ./quadratic.exe
Usage:
./quadratic.exe A B C
$ ./quadratic.exe foo bar baz
Invalid coefficient "foo"
```

But also we can give good input and it give us the roots based on what was
passed on the command line:

```
$ ./quadratic.exe 1 2 3
The roots of x^2 + 2x + 3 are:
(-2 + sqrt(-8)) / 2
(-2 - sqrt(-8)) / 2
$ ./quadratic.exe 2 3 4
The roots of 2x^2 + 3x + 4 are:
(-3 + sqrt(-23)) / 4
(-3 - sqrt(-23)) / 4
```

This is great. Now it's easier to test with different polynomials.

The problems with the code above are that it's repetitive and also that it
doesn't properly check for errors in the input. It detects if the first
character of the string is not a digit but if the string starts with digits
then it will accept the string and ignore the remaining digits:

```
$ ./quadratic.exe 1 2 s
Invalid coefficient "s"
$ ./quadratic.exe 1 2 4s
The roots of x^2 + 2x + 4 are:
(-2 + sqrt(-12)) / 2
(-2 - sqrt(-12)) / 2
```

This is just down to the way that `sscanf` works. The solution is to make our
own function that can validate the string. While we're at it we'll make a
function that can parse the string so we can reuse it:

``` C
int is_decimal_string(char str[])
{
    for(int i=0; str[i]!='\0'; i++)
    {
        if(!isdigit(str[i]))
        {
            return 0;
        }
    }
    return 1;
}

int parse_decimal_string(char str[])
{
    int num;
    if(!sscanf(str, "%d", &num))
    {
        fprintf(stderr, "Parsing bad string...\n");
        return -1;
    }
    return num;
}
```

We can now use these to rewrite the top of our `main` function as

``` C
int main(int argc, char *argv[])
{
    /* The three coefficients */
    int a, b, c;

    /* Parse command line arguments */
    if(argc != 4)
    {
        fprintf(stderr,
                "Usage:\n./quadratic.exe A B C\n");
        return 1;
    }
    for(int i=1; i<=3; i++)
    {
        if(!is_decimal_string(argv[i]))
        {
            fprintf(stderr,
                    "Invalid coefficient \"%s\"\n",
                    argv[i]);
            return 1;
        }
    }
    /* At this point the arguments are validated
     * so no need for further error checking */
    a = parse_decimal_string(argv[1]);
    b = parse_decimal_string(argv[2]);
    c = parse_decimal_string(argv[3]);
```

Now we still parse valid strings the same but have improved error checking for
invalid strings:

```
$ ./quadratic.exe 1 2 3
The roots of x^2 + 2x + 3 are:
(-2 + sqrt(-8)) / 2
(-2 - sqrt(-8)) / 2
$ ./quadratic.exe 1 2 3s
Invalid coefficient "3s"
```

At this point you may be thinking that actually including these functions is
just making our code longer. Sometimes using more functions does make the code
longer. Really what's important though is that the code is neatly modularised:
we want each individual function to be small and straight-forward so that our
code is easy to understand. We want to eliminate repetition so that there's
only one place we need to change the code when we want to change some aspect
throughout.

Really our `main` function is actually still too big. So we'll move all the
output into a function called `print_roots` and now our `main` function looks
like

``` C
int main(int argc, char *argv[])
{
    /* The three coefficients */
    int a, b, c;

    /* Parse command line arguments */
    if(argc != 4)
    {
        fprintf(stderr,
                "Usage:\n./quadratic.exe A B C\n");
        return 1;
    }
    for(int i=1; i<=3; i++)
    {
        if(!is_decimal_string(argv[i]))
        {
            fprintf(stderr,
                    "Invalid coefficient \"%s\"\n",
                    argv[i]);
            return 1;
        }
    }
    /* At this point the arguments are validated
     * so no need for further error checking */
    a = parse_decimal_string(argv[1]);
    b = parse_decimal_string(argv[2]);
    c = parse_decimal_string(argv[3]);
    print_roots(a, b, c);

    return 0;
}
```

We can go further but this is a reasonable size. We can easily see what the
main function does at a glance and it shows us a high-level view of what the
programme does. It checks to see that it has 3 integers as command line
arguments and prints an error message if not. Otherwise it parses the command
line arguments as integers and prints the roots of the corresponding
quadratic.

Download the code
-----------------

No doubt we can improve this programme further still and we will later. For
now you can download the current version here:

[Quadratic 2.0](quadratic-2.0.zip)
