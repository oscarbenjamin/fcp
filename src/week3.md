% EMAT10006 - Further programming
% Oscar Benjamin
% 8th Feb 2016

#

## Outline

- Arrays

- Strings

## Lists in Python

~~~~
>>> stuff = [1, 2, 3]   # Create a new list
>>> stuff
[1, 2, 3]
~~~~

~~~~
>>> stuff[1] = -1       # set list item
>>> stuff
[1, -1, 3]
>>> stuff[0]            # get list item
1
~~~~

~~~~
>>> stuff.append(5)     # change list size
>>> stuff
[1, -1, 3, 5]
~~~~

~~~~
>>> stuff[2] = 'Hello!' # Store different type of object
>>> stuff
[1, -1, 'Hello!', 5]
~~~~

## Arrays in C

- Collect similar objects like Python's lists

- Are *homogeneous* (only one type of item)

- *Cannot* be resized.

## Array example

~~~~
/* array.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  /* Declare and initialise array */
  int numbers[4] = {3, 10, 1024, -1};

  for(int i=0; i<4; i++)
  {
    printf("numbers[%d] = %d\n", i, numbers[i]);
  }
  return 0;
}
~~~~

[//]: # *]

~~~~
$ make
gcc -std=c99 -Wall array.c -o array.exe
$ ./array.exe
numbers[0] = 3
numbers[1] = 10
numbers[2] = 1024
numbers[3] = -1
~~~~

## Computer memory

![](images/task-manager.png)

## Computer memory

![](images/processes.svg)

## Process memory

![](images/process_memory.svg)

## Array in memory

![](images/array.svg)


~~~~
  /* Declare and initialise array */
  int vals[4] = {3, 10, 1024, -1};

  for(int i=0; i<4; i++)
  {
    printf("vals[%d] = %d\n", i, vals[i]);
  }
~~~~

## Arrays as memory blocks

- Compiler always needs to know the size (number of bytes) of any data type.

- An array of type `int[N]` uses `N*sizeof(int)` bytes (i.e. `N*4` bytes).

- An array uses a *contiguous* block of memory.

## Resizing arrays

- You can't resize arrays: the size must be fixed in the declaration.

- Usually choose a size that's big enough.

- Strange things happen if you go over the end of your array...

## Out of bounds access

~~~~
  int vals[] = {10, 11, 12}; // implicitly size 3

  for(int i=0; i<10; i++)
  {
    printf("vals[%d] = %d\n", i, vals[i]);
  }
~~~~

~~~~
vals[0] = 10
vals[1] = 11
vals[2] = 12
vals[3] = 3     <--- i
vals[4] = 0     <--- other stuff
vals[5] = 0
vals[6] = 675862381
vals[7] = 32655
vals[8] = 0
vals[9] = 0
~~~~

## Out of bounds access

![](images/array2.svg)

~~~~
vals[0] = 10
vals[1] = 11
vals[2] = 12
vals[3] = 3     <--- i
vals[4] = 0     <--- other stuff
vals[5] = 0
vals[6] = 675862381
vals[7] = 32655
vals[8] = 0
vals[9] = 0
~~~~

## Array indices

- If `a` is an array of size `N` e.g. `int a[N]` then valid indices are `0`,
  `1`, `2`, ..., `N-1`.

- Negative indices are *always* invalid.

- The compiler cannot check that you are using valid indices: you need to do
  it.

- Common source of beginner problems.

## Comparing arrays

~~~~
  int numbers1[4] = {3, 4, 5, 6};
  int numbers2[4] = {3, 4, 5, 6};

  if(numbers1 == numbers2)
  {
    puts("numbers1 and numbers2 are equal.");
  }
  else
  {
    puts("numbers1 and numbers2 are NOT equal!");
  }
~~~~

~~~~
$ make
gcc -std=c99 -Wall compare.c -o compare.exe
$ ./compare.exe
numbers1 and numbers2 are NOT equal!
~~~~

## Array equality

- Comparing arrays with `==` doesn't work.

- An array is only ever equal to itself.

- You probably want to check that both arrays have equal *elements*.

## Compare array elements

~~~~
  int numbers1[4] = {3, 4, 5, 6};
  int numbers2[4] = {3, 4, 5, 6};

  int equal = 1;
  for(int i=0; i<4; i++)
  {
    if(numbers1[i] != numbers2[i])
    {
      equal = 0;
      break;
    }
  }
~~~~

~~~~
$ make
gcc -std=c99 -Wall compare2.c -o compare2.exe
$ ./compare2.exe
numbers1 and numbers2 are equal.
~~~~

#

## Strings in C.

- In C a string is just an array of type `char`.

- `char` is short for *character*

- But `char` is an integer type...

## ASCII code

![](images/asciifull.gif)

## Hello world in ASCII

`'H'`(72), `'e'`(101), `'l'`(108), `'o'`(111), `' '`(20), ...
`'W'`(87), `'r'`(114), `'d'`(100), `'!'`(21), `'\n'`(10), ...

~~~~
>>> ord('a')
97
>>> chr(97)
'a'
~~~~

~~~~
>>> nums = [ord(c) for c in "Hello World!\n"]
>>> nums
[72, 101, 108, 108, 111, 32, 87, 111, 114, 108, 100, 33, 10]
>>> chars = [chr(n) for n in nums]
>>> chars
['H', 'e', 'l', 'l', 'o', ' ', 'W', 'o', 'r', 'l', 'd', '!', '\n']
~~~~

## Hello world in ASCII

~~~~
/* asciihello.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  char message[] = {
    72, 101, 108, 108, 111,     /* "Hello" */
    32,                         /* " "     */
    87, 111, 114, 108, 100, 33, /* "World" */
    10,                         /* "!"     */
    0                           /* "\0"    */
  };

  puts(message);
  return 0;
}
~~~~

[//]: # *]

~~~~
$ make
gcc -std=c99 -Wall asciihello.c -o asciihello.exe
$ ./asciihello.exe
Hello World!
~~~~

## Initialising strings

~~~~
  char message1[] = {72, 101, 108, 108, 111, 32,
                87, 111, 114, 108, 100, 33, 10, 0};

  char message2[] = {'H', 'e', 'l', 'l', 'o', ' ',
                'W', 'o', 'r', 'l', 'd', '!', '\n', '\0'};

  char message3[] = "Hello World!\n";
~~~~

These are equivalent.

- `'H'` is an integer expression equivalent to `72` or `0x48`.

- `'H'` is easier to understand though: readability counts!

## Strings in C

- A string is a *null-terminated* `char` array.

~~~~
  char message[] = "Hello World!\n";

  int i = 0;
  while(message[i] != '\0') /* Check for null zero */
  {
    i++;
  }
  printf("\"%s\" has %d characters...\n", message, i);
  printf("Plus one null-byte.\n");
~~~~

~~~~
$ make
gcc -std=c99 -Wall string_length.c -o string_length.exe
$ ./string_length.exe
"Hello World!
" has 13 characters...
Plus one null-byte.
~~~~

## The `<string.h>` header

~~~~
/* strlen.c */

#include <stdio.h>
#include <string.h>  /* String functions */

int main(int argc, char *argv[])
{
  char string[] = "A moderately long string";

  int length = strlen(string);

  printf("Length: %d\n", length);

  return 0;
}
~~~~

[//]: # *]

~~~~
$ make
gcc -std=c99 -Wall strlen.c -o strlen.exe
$ ./strlen.exe
Length: 24
~~~~

## Comparing strings.

~~~~
  char string1[] = "foo";
  char string2[] = "bar";

  if(!strcmp(string1, string2))
  {
    printf("string1 and string2 are the same\n");
  }
  else
  {
    printf("string1 and string2 are different\n");
  }
~~~~

~~~~
$ make
gcc -std=c99 -Wall strcmp.c -o strcmp.exe
$ ./strcmp.exe
string1 and string2 are different
~~~~

`strcmp` returns `0` (false) if the strings are equal and *non-zero* otherwise!

## Parsing decimal strings

~~~~
  char string[] = "123";

  int a;
  if(!sscanf(string, "%d", &a))
  {
    fprintf(stderr, "Could not parse the string...");
    return 1; // Indicate error somehow...
  }

  printf("a = %d    2 * a = %d\n", a, 2 * a);
~~~~

~~~~
$ make
gcc -std=c99 -Wall parse.c -o parse.exe
$ ./parse.exe
a = 123    2 * a = 246
~~~~


## Parsing command line

~~~~
/* argparse.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  /* Parse the command line */
  int num;
  if(argc != 2 || !sscanf(argv[1], "%d", &num))
  {
    fprintf(stderr, "Please give a valid number\n");
    return 1;
  }

  printf("You entered %d and %d^2 = %d\n", num, num, num*num);

  return 0;
}
~~~~

~~~~
$ make
gcc -std=c99 -Wall argparse.c -o argparse.exe
$ ./argparse.exe
Please give a valid number
$ ./argparse.exe 12
You entered 12 and 12^2 = 144
~~~~


# That's all folks...

(See you on Friday)
