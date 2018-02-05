Strings in C
============

We've been using strings for a while now but one thing that we've overlooked
is how they actually work in C. In Python it's not necessary to know how the
work but in C it is. So here it is: in C a string is an array of type `char`.
Everything that I've said about arrays also applies to strings. Here's how to
rewrite our original hello world program so that it uses a string variable
instead of just a literal:

``` C
/* hello.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  char message[] = "Hello world!";

  puts(message);

  return 0;
}
```

This gives the same output as before:

```
$ make
gcc -std=c99 -Wall hello.c -o hello.exe
$ ./hello_variable.exe
Hello world!
```

So we see that in the same way that a decimal literal e.g. `123` represents an
object of type `int` a string literal e.g. `"Hello"` represents an object of
type `char[]`. A string is an array of type `char`.

We saw before that `char` is a signed integer type so what does it mean that a
string is an array of type `char`? The most commonly used characters are given
integer numbers from 1 to 127 and these are used to represent each character.
We can also initialise a string using the array initialiser syntax that we
have already seen. These three are quivalent:

``` C
  char message1[] = {72, 101, 108, 108, 111, 32,
                87, 111, 114, 108, 100, 33, 10,
                0};

  char message2[] = {'H', 'e', 'l', 'l', 'o', ' ',
                'W', 'o', 'r', 'l', 'd', '!', '\n',
                '\0'};

  char message3[] = "Hello World!\n";
```

Notice that we need to put an extra `'\0'` character at the end of the array.
This is called the null zero (or null byte) and is added implicitly when we
write a string literal with double quotes `""`. The `'\0'` character has
integer value `0` and is used to mark the end of the string. I said before
that in C when we pass an array into a function then the function cannot know
the size of the array. That's why we would normally give each function that
takes an array an additional argument to tell it the size of an array. We
don't need to do that with strings because they are *null-terminated*.

For example the `puts` function above will know that the end of the string
occurs when it finds the `'\0'` character. There is another function called
`putc` (short for put Character) which prints an individual character. The
`puts` function essentially works like this:

``` C
int puts(char string[])
{
  int i = 0;

  // Loop until we hit the null zero
  while(string[i] != '\0')
  {
    // Print current character
    fputc(string[i], stdout);
    i++;
  }
  return 1; // indicate success
}
```

Actually if an error occurs while printing then the `puts` function may return
something other than 1 and it's more complicated than this but the basic idea
is that it finds the end of the string by looking for the null byte.

If we declare the size of a string then we need to add 1 to account for this
null byte. If a string has `n` characters then it needs `n+1` bytes of memory.

``` C
  char message[6] = "hello";
```

However we don't normally need to explcitly declare the size since the
compiler can do that for us.

String functions
----------------

There are a number of functions provided in the `string.h` header that are
useful for working with strings.

``` C
/* string.c */

#include <stdio.h>
#include <string.h> // <-- string functions

int main(int argc, char *argv[])
{
  char message[] = "Hello world!";

  // strlen finds the length of a string
  int length = strlen(message);

  printf("The string is \"%s\"\n", message);
  printf("It has %d characters\n", length);
  printf("It needs %d bytes\n", length + 1);

  char name1[] = "dave";
  char name2[] = "john";

  // Compare the two strings
  int compare = strcmp(name1, name2);

  if(compare == 0)
  {
    printf("The strings are equal\n");
  }
  else // compare != 0
  {
    printf("The strings are not equal\n");
  }

  // Shorthand - notice the ! below
  if(!strcmp(name1, name2))
  {
    printf("The strings are equal\n");
  }
  else
  {
    printf("The strings are not equal\n");
  }

  return 0;
}
```

The above program demonstrates the two most commonly used string functions in
C. `strlen` returns the number of characters in a string. Note that this is
one less than the number of bytes of memory used by the string because we also
need a byte for the null zero.

`strcmp` is used to compare two strings (writing `a == b` does NOT work). The
return value from `strcmp` is zero if the two strings are equal and non-zero
otherwise. This means that if we want to test if two strings are equal we
usually write `if(!strcmp(str1, str2))` where the `!` symbol is the logical
not operator.

Exercise - print the ascii characters
-------------------------------------

We can print an ASCII number and corresponding character with something like

``` C
    printf("ASCII num: %d  character '%c'", 65, 65);
```

Make a program that prints all the ASCII characters from 1 to 127. Take a
careful look at the output.

Exercise - upper and lower case
-------------------------------

There is a function called `isupper` declared in the `ctype.h` header which
you can use:

``` C
#include <ctype.h> // <-- for isupper

int main(int argc, char *argv[])
{
    char string = "Hello";

    if(isupper(string[0]))
    {
      // First character is upper case A-Z
    }
    else
    {
      // First character is something else.
    }
    return 0;
}
```

The `ctype.h` header defines a bunch of functions. `isupper` determines if a
character is upper case and returns non-zero if it is. `islower` determines if it
is lower case (these are not mutually exclusive!). We also have `tolower` which
takes a character and returns the same character unless it is an upper case
character. If it is an upper case character it will return the lower case
equivalent. Study these functions by testing different values e.g.:
`tolower('a')` and `tolower('A')` or `tolower('$')`, `isupper('+')` etc. Make sure you
understand what they do and then try to write your own version of each of
these functions using the ASCII numbers for the different characters.

To work with ASCII codes remember that it's just integer arithmetic. A
character with ASCII number $n$ is upper case if $65 \le n \le 90$. In C we can
spell that as `'A' <= n && n <= 'Z'` since `'A'` is equivalent to `65`.
Similarly all the lower case characters are 32 greater than their upper case
equivalents so e.g. `'a' == 'A' + 32`. We could convert `c` to lower case with
something like this `c + ('a' - 'A')` This gives us an easy arithmetic way
to convert a character from lower to upper case or vice-versa.

Exercise - digits
-----------------

Often we have strings representing numbers e.g. "123". Note that the
ASCII number for the character "1" is *not* 1. Make two functions `is_digit`
and `to_int` that can check if a character is a digit character (0-9) and
convert it to its integer value. We should be able to use these functions in a
programme like this:

``` C
/* digits.c */

#include <stdio.h>
#include <string.h>

int is_digit(char c);
int to_int(char c);

int main(int argc, char *argv[])
{
  char string[] = "I saw 123 things";

  int length = strlen(string);
  int total = 0;

  for(int i=0; i<length; i++)
  {
    if(is_digit(string[i]))
    {
      total += to_int(string[i]);
    }
  }

  printf("The sum of the digits is %d\n", total);

  return 0;
}

// Insert function definitions here:
```

The output from the program should be:

```
$ make
gcc -std=c99 -Wall digits.c -o digits.exe
$ ./digits.exe
sum of digits in "I saw 123 things" is 6
```

Note that in practice (when not doing this exercise) you should use the
functions such as `isdigit` which are already provided for you rather than
writing your own.

Parsing strings
---------------

Parsing a string means processing it to extract data. Parsing is a very common
part of normal programming. We might do this because we have a string e.g.
`"123"` and we want to multiply it by 2. We can't multiply strings so we first
convert it to the integer value `123` and then multiply that by 2.

The standard way to parse strings in C is to use the `sscanf` function. This
works a bit like `printf` but in reverse:

``` C
/* parse.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  char string[] = "123";

  int number;
  if(!sscanf(string, "%d", &number))
  {
    fprintf(stderr, "Could not parse the string...");
    return 1;
  }
  printf("2 * %s = %d\n", string, 2 * number);

  return 0;
}
```

This gives us:

```
$ make
gcc -std=c99 -Wall parse.c -o parse.exe
$ ./parse.exe
2 * 123 = 246
```

So the way that this works is that we give two strings to `sscanf`. The first
is the string that we want to parse. The second is a *format* string. The
format string is exactly like the format string that we give to `printf` and
contains some characters but also some special codes e.g. `"%d"`. For `printf`
the `"%d"` code marks a place where the decimal representation of an integer
variable should be inserted.  For `sscanf` the `"%d"` code marks a part of the
string where there is a decimal representation of an integer that we want to
extract so that it can be stored in a variable. In the example above we parse
the string `"123"` with the format string `"%d"` and store the result in
`number`. The `&` is used in `&number` to pass the memory *address* of the
variable `number` to the `sscanf` function. The function will use that address
to modify the variable `number`.

We can use more complicated format strings. In the example above we could
parse a string `"stuff: 123"` using a format string `"stuff: %d"` and it would
still work. If the format string has more than one `%` code then the return
value from `sscanf` is the number of items successfully parsed. So we need to
check that matches the number of items actually expected. For example we
could do

``` C
    int return_code = sscanf("12:34:mm", "%d:%d:%d",
                            &hours, &minutes, &seconds);
```

In this case `sscanf` will succeed in parsing `12` into `hours` and then the
`':'` matches so it will parse `34` into `minutes`. Then it expects to be able
to parse a decimal string for `seconds` but finds the letters `"mm"`. This
causes a parse failure. Since 2 items were successfully parsed `sscanf`
returns `2`. You can check this by printing out the value of `return_code`.

If we wanted to check that all items were successfully read we could do
something like:

``` C
    if(sscanf("12:34:mm", "%d:%d:%d",
              &hours, &minutes, &seconds) != 3)
    {
      fprintf(stderr, "Invalid string\n");
      return 1; // failure
    }
```

Exercise - sscanf
-----------------

Play around with `sscanf`. Make sure that you understand how it works, when it
works, and what happens when it fails. Make sure that you always check the
value it returns (and not just in this exercise!).

Command line arguments
----------------------

We've seen `argv` sitting there as the second argument to our `main` function
for some time now. I haven't explained how to do this yet but `argv` is an
*array of strings*. Since each string is an array of type `char`, `argv` is an
array of arrays of type `char`. I won't talk about how to create such an array
but know that `argv[0]` is a string (array of `char`) and so is `argv[1]` etc.
The size of the `argv` array is given by `argc`. So what are the strings given
in `argv`? They are the command line arguments given to the program. Every
program that you've used `make`, `gcc`, `ls`, etc. takes command line
arguments and they do this by inspecting the value of `argv`. Here's a short
program that shows how to access the strings in `argv`:

``` C
/* args.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  printf("You entered %d arguments\n", argc);
  for(int i=0; i<argc; i++)
  {
    printf("argv[%d] is %s\n", i, argv[i]);
  }
  return 0;
}
```

We can run this to get:

```
$ make
gcc -std=c99 -Wall args.c -o args.exe
$ ./args.exe 
You entered 1 arguments
argv[0] is ./args.exe
$ ./args.exe foo bar baz
You entered 4 arguments
argv[0] is ./args.exe
argv[1] is foo
argv[2] is bar
argv[3] is baz
```

Now look carefully at that. We can see that the program prints out different
things depending on the arguments typed on the command line in the shell when
running the program. This is exactly what we want our programs to be able to
do so that we don't have to recompile them all the time just to change some
numbers or something.
