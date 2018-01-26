% EMAT10006 - Further programming
% Oscar Benjamin
% 25th Jan 2016

#

## Outline

- Build on the basics from EMAT10007
- Explore two new programming languages
- Learn additional computing skills/knowledge

## Lectures and times

- Lectures 1000 Mondays and 2-hour labs on Friday mornings.
- You are expected to also work outside of those times!
- 5 weeks of C with me (Oscar Benjamin)
- 6 weeks of Java with Trevor Martin

## Assessments

Assessment   Deadline    Weight
----------   --------    ------
Basic C test week 14     4%
C final       week 19     48%
Java final    week 24     48%

## C deadlines

Assessment   Deadline     Weight
----------   --------     ------
Basic C test week 14      4%
C part 1     week 15      0%
C part 2     week 16      0%
C part 3     week 17      0%
             reading week
C final       week 19      48%

## First part:

Week/date  Lecture and assignments
---------  --------
Week 13    C
Week 14    C - Basic C test
Week 15    C - Part 1 assignment
Week 16    C - Part 2 assignment
Week 17    C - Part 3 assignment
Week 18    Reading week (no lecture or lab)
Week 19    Java - C final assignment due Thursday.


## Second part:

Week/date  Lecture and assignments
---------  --------
Week 19    Java - C final assignment due on Thursday.
Week 20    Java - Part 1
Easter     (3 week break)
Week 21    Java
Week 22    Java - Part 2
Week 23    Java
Week 24    Java - (no lecture - bank holiday) Main Java assignment due Friday

## Plagiarism

We had problems with plagiarism the year before last:

- Fine to work together when not doing assignments
- Please do NOT work together or share code when you are
- (We will check for plagiarism)

More on this later...

#

## Popular programming languages

![](images/top10.png){width=100%}

(From tiobe.com)

## Family tree

![](images/evo-prog-lang.png){width=100%}

## C

- Old but still widely used.
- Standard for working on *any* hardware.
- Core components in your computer

## Things written in C/C++

- Windows/OSX/Linux (most operating systems)
- Browsers: Chrome, Firefox etc.
- High end computer games.
- Python

Python is actually a C program!

## When C is good

- Fast programs
- Embedded systems
- Operating systems
- Core lower-level applications
- Program basic hardware (arduino etc)
- Need to run on specific hardware/OS

## When C is bad

- Need to quickly make a program
- Speed (of program) is unimportant
- Simple high-level applications
- Need to run on wide range of hardware/OS

# Demo Python/C...

#

## Compilers and interpreters

Python is *interpreted* and C is *compiled* but what does that mean?

- Human readable code. This is the stuff that humans write and look at.
- Machine code. This is what the computer can actually execute.

## Compilation

A compiler converts human code to machine code e.g.:

Human code:

~~~~~
int multiply(int a, int b)
{
  int c = a * b;
  return c;
}
~~~~~

Machine code (and assembly):

~~~~~ x86asm
   0: 55                    push   %rbp
   1: 48 89 e5              mov    %rsp,%rbp
   4: 89 7d ec              mov    %edi,-0x14(%rbp)
   7: 89 75 e8              mov    %esi,-0x18(%rbp)
   a: 8b 45 ec              mov    -0x14(%rbp),%eax
   d: 0f af 45 e8           imul   -0x18(%rbp),%eax
  11: 89 45 fc              mov    %eax,-0x4(%rbp)
  14: 8b 45 fc              mov    -0x4(%rbp),%eax
  17: 5d                    pop    %rbp
  18: c3                    retq
~~~~~

## Compilation in C

Running C code is a two-step process:

- Compile the C code to machine code
- Run the compiled code

The computer can execute machine code directly (fast)

## Python is interpreted

When you run your Python program it is not compiled to machine code.  The
interpreter reads the code and simulates executing it.

The interpreter itself is a C program that was compiled to machine code (by
someone else).

## The main assignment

The main assignment for this unit will be to write an assembly decompiler.

Need to pay attention to the stuff about machine code!

# Demo ZX256...

# Students not in EMAT or EEE

- There are students here taking this as an optional unit who may not have done
  the prerequisite unit
- School of Maths students I'm talking about you!
- Wait at the end and see me...

# That's all folks...

(See you on Friday)
