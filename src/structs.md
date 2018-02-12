Structs
=======

Structs in C are a way to create a composite data type out of elementary
data types. This is similar to creating a *class* in languages such as Python
but works in a lower-level way.

A simple example:

``` c
/* struct.c */

#include <stdio.h>

struct Vector3D
{
  int x;
  int y;
  int z;
} typedef Vec3D; // <-- this is the name

int norm2(Vec3D vector);

int main(int argc, char *argv[])
{
  Vec3D some_vector = {3, 4, 1};
  printf("some_vector has norm %d\n", norm2(some_vector));
  return 0;
}

int norm2(Vec3D vector)
{
  return vector.x * vector.x
       + vector.y * vector.y
       + vector.z * vector.z;
}
```

the output from this is

```
$ ./struct.exe
some_vector has norm 26
```

So what happened there? We use the `struct` statement to define a struct. Our
struct represents a 3D vector and has two names `Vector3D` and `Vec3D`. The
reason that it has two names is just a strange historical artefact of the way
that C has evolved over the years. The first name can be anything for our
purposes as we will only use the second name `Vec3D`.

In the body of the struct we list variables each having a type e.g. `int` and
a name e.g. `x`. In this particular case our struct defines a 3D vector with 3
integer coordinates called `x`, `y`, and `z`. These are the members of the
struct.

Having defined a struct `Vec3D` we can use this name just like `int` or `char`
or any other data type.  So we can declare a function called `norm2` which has
an argument of type `Vec3D`. A variable of type `Vec3D` has members called
`x`, `y` and `z`. In the definition of the function `norm2` the argument of
type `Vec3D` is called `vector`. We can access e.g. the `x` member of the
struct with the expression `vector.x`.

We can declare a variable of type `Vec3D` e.g. `Vec3D some_vector`. Since the
struct has 3 members and each of those needs 4 bytes of storage space the
total space needed for `some_vector` is 12 bytes (on some systems it may end
up using more space because of *alignment* but I don't want to explain that
here).

Multiple struct variables
-------------------------

In the example above the struct doesn't seem like much of an improvement over
just using 3 separate variables. One way in which structs start to become more
useful is if we have many struct variables of the same type. In our example
each can group together its 3 coordinates keeping them as a single object.
Let's extend the above example to have two vectors and a dot product function:

``` c
/* dot.c */

#include <stdio.h>

struct Vector3D
{
  int x;
  int y;
  int z;
} typedef Vec3D; // <-- this is the name

int dot(Vec3D a, Vec3D b);

int main(int argc, char *argv[])
{
  Vec3D vec1 = {3, 4, 1};
  Vec3D vec2 = {-1, 2, 4};
  printf("vec1 dot vecb = %d\n", dot(vec1, vec2));
  return 0;
}

int dot(Vec3D a, Vec3D b)
{
  return a.x * b.x + a.y * b.y + a.z * b.z;
}
```

We can even have an array of structs e.g. `Vec3D vectors[10]` and we can
initialise this by combining struct initialisation syntax with array
initialisation syntax:

``` c
   Vec3D vectors[3] = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
```

Exercise
--------

Extend the above by adding a cross product function and a function to print a
vector displaying it as e.g. `(1, 2, 3)`.

Pointers to structs
-------------------

Just as we can have pointers to other variables we can also have pointers to
structs. We declare them in much the same way but a pointer to a struct gives
us a special syntax for accessing the members of the struct. A short example
is

``` c
/* pstruct.c */

#include <stdio.h>

struct Vector3D
{
  int x;
  int y;
  int z;
} typedef Vec3D;

int main(int argc, char *argv[])
{
  Vec3D vec = {3, 4, 1};

  Vec3D *pvec = &vec;

  // These two are equivalent
  printf("(*pvec).x = %d\n", (*pvec).x);
  printf("pvec->x   = %d\n", pvec->x);

  return 0;
}
```

When we run this we get
```
(*pvec).x = 3
pvec->x   = 3
```

This shows the two equivalent ways to access the member of a struct through a
pointer to the struct. After `pvec = &vec` we can think of `*pvec` and `vec`
as equivalent. So naturally `vec.x` and `(*pvec).x` are equivalent. The
brackets don't mean anything special here - they just clarify the order of the
operations: we want to dereference the pointer and *then* access the `x`
member of the result, rather than the other way round.

Since structs and pointers to structs are so common in C there is a shorthand
notation for this. The two expressions `(*pvec).x` and `pvec->x` are
equivalent. The arrow `->` notation is preferred though. It is not uncommon to
have a struct with a member that is a pointer to another struct (and so on).
In that case the reason for preferring one notation over the other is clearer:

``` c
pstruct1->pstruct2->x = 1;  // <--- preferred
(*(*pstruct1).pstruct2).x = 1;
```

------------
Next section: [fileio](fileio.html)
