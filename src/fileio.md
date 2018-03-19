File IO
=======

Most real programs need to do stuff with files. There are basically two things
you can do with files: you can *read from* them or you can *write to* them.
These are analogous to the load/save commands that you're probably used to
using in graphical programs. Many programs don't have a graphical interface
and in fact their sole purpose is to read one file and use it to write
another. A good example of this is the compiler `gcc` which we have been using
to compile our C programs. When we type

```
$ gcc hello.c -o hello.exe
```

we are running `gcc` and telling it that the input file is `hello.c` and the
output (`-o` for output) should be a file called `hello.exe`. The `gcc`
program will read the file `hello.c` line by line, compile the C code to
machine code and write the output to the file `hello.exe`. Actually there's a
few more stages than this in what `gcc` does but it doesn't matter much right
now. This concept of input/output is so common and generic in computing that
it is typically abbreviated to "IO". This is why the header file we've been
using all along is called `stdio.h` which is short for STandarD Input Output.

Character by character
----------------------

In some cases the simplest way to do IO is to read and write individual
characters. C provides two functions for doing this: `fgetc` and `fputc`:

``` c
/* chario.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  /*
   * Open both files checking for success.
   */
  char infilename[] = "foo.txt";
  char outfilename[] = "bar.txt";

  FILE* infile = fopen(infilename, "r"); // read
  if(infile == NULL)
  {
    fprintf(stderr, "Unable to open \"%s\"\n", infilename);
    return 1;
  }

  FILE* outfile = fopen(outfilename, "w"); // write
  if(outfile == NULL)
  {
    fprintf(stderr, "Unable to open \"%s\"\n", outfilename);
    fclose(infile);
    return 1;
  }

  /*
   * read each character of infile and write to outfile
   */
  char character;
  for(;;) // <-- infinite loop
  {
    /*
     * Read character and check for End Of File
     */
    character = fgetc(infile);
    if(character == EOF)
    {
      break; // exit loop
    }

    /* convert to lower case */
    if('a' <= character && character <= 'z')
    {
      character -= 'a' - 'A';
    }

    /* Write to hte output file */
    fputc(character, outfile);
  }

  fclose(infile);
  fclose(outfile);
  return 0;
}
```

(For the above program to work you first need to create a file called
"foo.txt" and put some text in it.)

Note that there's a few stages to using files here. First before we can
read/write we need to *open* the files with `fopen`. When calling `fopen` we
ask for either read `"r"` or write `"w"` permissions. We need to check the
success of the `fopen` function. Like many functions which return a pointer
the `fopen` function returns `NULL` on failure. We must not attempt to
read/write the files in this case so we print a message and exit the program.
When we're done reading/writing the files we need to close them with `fclose`.

Between `fopen` and `fclose` we can call read/write functions on the file
objects. In this case we're using `fgetc` to read one character at a time from
the file. We can also use `fputc` to write one character to the output at a
time. This program reads the input character, converts it to upper case and
then writes it to the output file. It does this in a loop until `fgetc`
returns `EOF` which is a special value indicating that all the characters have
been read from the file and we are now at the End Of the File.

Line by line
------------

Reading a file character-by-character is tedious for many applications and can
also be inefficient. It's more common to read in larger chunks and there are
two main ways to do this depending on whether the file is a *text file* or a
*binary file*. Text files contain bytes representing ASCII characters and are
typically separated into *lines*. A line is a sequence of characters that ends
with a newline character. Binary files are not usually organised in lines and
can have any structure for the bytes inside. When reading text files the lines
have some logical meaning so it is common to write a program that reads such a
file line by line. For that C provides the functions `fgets` and `fputs`:

``` c
/* fgets.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  char line[256];
  int line_number = 1;

  FILE* inputfile = fopen("fgets.c", "r");
  if(inputfile == NULL)
  {
    fprintf(stderr, "Unable to open file\n");
    return 1;
  }

  while(fgets(line, 256, inputfile) != NULL)
  {
    printf("%d: %s", line_number, line);
    line_number++;
  }
  fclose(inputfile);
  return 0;
}
```

Note that when calling `fgets` we pass it an array (called `line` in this
example). The function will read bytes from the file and copy them into the
array. It stops wen it finds a newline character in the file (or EOF or an
error). `fgets` will return `NULL` when it gets to the EOF so we can use that
as a condition to terminate our loop that reads lines. We also need to tell
`fgets` how big our array is (256 in this example) so that it doesn't overfill
the array. We choose the size of the array to be big enough that we will
usually be able to fit a whole line in. Note that the loop above replaces the
contents of `line` on each iteration: at the end of the program `line` will
only store the last line that was read from the file.

Chunk by chunk
--------------

The other way to read files is in fixed size chunks. This is the most common
way to read a binary file and often the contents of a binary file are
arranged in chunks of some known size. For this C provides the functions `fread` and `fwrite`
which will read/write fixed size chunks. Let's say for example that we wanted
to copy a file (just like the `cp` command). The standard way would be to read
in chunks that are not too large or too small (4096 bytes is a reasonable
size) and write each chunk to the output file. We can do that like so:

``` c
/* chunks.c */

#include <stdio.h>

int main(int argc, char *argv[])
{
  /* input args should be source and destination */
  if(argc != 3)
  {
    fprintf(stderr, "Usage: ./chunks.exe INPUTFILE OUTPUTFILE\n");
    return 1;
  }
  char* infilename = argv[1];
  char* outfilename = argv[2];

  FILE* infile = fopen(infilename, "rb"); // read
  if(infile == NULL)
  {
    fprintf(stderr, "Unable to open \"%s\"\n", infilename);
    return 1;
  }

  FILE* outfile = fopen(outfilename, "wb"); // write
  if(infile == NULL)
  {
    fprintf(stderr, "Unable to open \"%s\"\n", infilename);
    fclose(infile);
    return 1;
  }

  /*
   * Loop until read less than 4096 bytes.
   */
  int bytes_read;
  char chunk[4096];
  for(;;)
  {
    bytes_read = fread(chunk, 1, 4096, infile);
    fwrite(chunk, 1, bytes_read, outfile);
    if(bytes_read < 4096)
    {
      break;
    }
  }

  fclose(infile);
  fclose(outfile);
  return 0;
}
```

So in the example above we see that we call `fread` with 4 arguments which
are: the array we want to read into (`chunk`), the size of the elementary
type that we're trying to read (e.g. 1 byte for `char`), the number of
elements we want to read (4096) and the file object we want to read from.
`fread` will then read $1\times 4096$ bytes from the file referred to by
`infile` and store them in the array `chunk`.

`fread` returns the number of bytes actually read from the file which may be
less than we requested if e.g. we've reached the end of the file. `fwrite`
takes all the same arguments but writes the bytes currently in the array
`chunk` out to the file referred to by `outfile`. We tell `fwrite` to write
the number of bytes that `fread` said it read. `bytes_read` will be 4096 after
reading each chunk except for the last iteration when we've reached the end of
the file.

Exercises
---------

Test out the programs above. Compile them, try them with different input files
and make sure that you understand what they do.
