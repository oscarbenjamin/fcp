Feedback
========


Long main function
------------------

Your main function should be made more succint. Ideally, the main function
consists of a series of conditional statements (or a case switch) which use
the command line arguments supplied by the user to direct the flow of the
program to functions outside of main, where the appropriate functionality has
been implemented. There should not be any of the actual functionality of the
program, or complicated parsing of the inputs, inside the main function.  An
example of a main function for a simple program, implemented in this way, can
be found on blackboard.


Poor control flow
-----------------

You need to work on improving the control flow of your program. 'Control flow'
is the order in which blocks of code are executed within your program, and is
decided by the conditional statements, loops, function calls, etc. you write.
Bad control flow is usually overly-complicated or includes unclear
conditionals, making it difficult to debug, and hard for a reader of your code
to understand what happens next. Signs of bad control flow include: having
lots of nested if/else statements, nested loops and having very long
functions. To improve your control flow, carefully plan out functions before
you write them. Think through the different possible ways in which it might
execute (and what could go wrong!), and try to come up with the simplest
possible control flow to manage all of them.


Too few comments
----------------

You need to include more comments in your code. Comments should be used to
help people looking at your code understand what is being done. This includes
yourself, if you have to come back to old code in the future, or other people
(in this case, it is your marker, but in the real world this will be
colleagues, customers, users etc.). Ideally every function should have a short
comment description just above it, explaining what it does, and perhaps some
mention of its possible return values or its inputs. Inline comments should be
kept short (ideally one line) and should be used to explain lines / blocks of
code whose purpose is not immediately obvious. They should not be used
excessively where the code is simple to understand.


Too many comments
-----------------

You have included excessive comments in your code. Although including comments
is an essential part of writing good code, too many makes code difficult to
read and wastes lines on the screen. Ideally every function should have a
short comment description just above it, explaining what it does, and perhaps
some mention of its possible return values or its inputs. Inline comments
should be kept short (ideally one line) and should be used to explain lines /
blocks of code whose purpose is not immediately obvious. They should not be
used excessively where the code is simple to understand, for example in the
declaration of variables or conditional statements where the condition is
obvious.


Poor names for variables and functions
--------------------------------------

You need to improve the naming of your variables and functions. An essential
part of writing readable code is giving your variables and functions
appropriate names, and being consistent with those names. `'x', 'num', 'temp',
'integer'` are (in almost all contexts) bad names for variables, as they do
not convey enough information about what the variable represents or is used
for. When it is in context, I should know basically what a variable does just
from its name. Function names should describe very briefly what the function
does, and so starting them with a verb is often good practise; for example:
`check_column_data()`, `set_initial_value()` and `get_input_file()`. Do not be
afraid to use longer names sometimes if it helps the readability of the code.


Compiler warnings
-----------------

When I compile your code the compiler produces warnings to the terminal.
Although it will still compile the code, these warning are produced for a
reason! Always try to fix warnings as they appear. Especially in a low-level
language like C, seemingly irrelevant warning messages (especially about
pointers, casts or memory) may help you avoid or even fix difficult bugs.


Functions
---------

You need to make better use of functions. (I will expand this section!)


Duplication
-----------

Your code has a lot of duplication. Better use of functions or a restructuring
of the control flow would reduce this (insert examples!!!).


Indentation
-----------

Your code is not correctly indented (examples!!)


Style
-----

You should use a consistent style throughout. (examples!!)


Long lines
----------

Done have very long lines of code. (examples!!)
