# Call user function I

We have got an idea how compilation and linking work when calling standard C function via [call-clib-function](../call-clib-function/README.md). Now we explore how to call a user developed function.

The user developed function `foo` is declared in header file [include/foo.h](./include/foo.h), and implemented in source file [src/foo.c](./include/foo.h). Since source file [main.c](./main.c) calls `foo`, it needs to include file `foo.h` as well.

```
+ -- main.c
+ -- include
|    + -- foo.h
+ -- src
     + -- foo.c
```

> It is a convention to put header files in folder `include` and source C files in folder `src` for better organization.

Let's get started.

## Compile

We stay in the folder hosting `main.c`. Run the following command on terminal to save the folder's absolute path into an environment variable `includePath`:

```
includePath="`pwd`/include"
```

> Command `pwd` (notice it is backtick, not quote) yields a string representing the absolute path of current folder, ie if you are in folder `/usr/home/foo`, the command yields "/usr/home/foo".

### Generate object file for `foo.c`

If we run command `gcc -c src/foo.c` to try to compile `src/foo.c`, we will see compilation error:
```
src/foo.c:1:10: fatal error: 'foo.h' file not found
#include <foo.h>
         ^~~~~~~
1 error generated.
```

Obviously, compiler cannot find header file `foo.h`. The fix is to pass flag `-I` into compiler to tell where the header file could be:
```
gcc -c -I${includePath} src/foo.c
```

Compilation would pass and you would see `foo.o` generated.

### Generate object file for `main.c`

Similar to compiling `foo.c`, you cannot compile `main.c` by running `gcc -c main.c` since the compiler could not find `foo.h`. You have to tell compiler where the header file could be:
```
gcc -c -I${includePath} main.c`
```

You will see `main.o` generated successfully.

## Link

With object files for both `main.c` and `foo.c` ready, you can link them to generate executable:
```
gcc main.o foo.o
```

Basically, you tell linker there are two object files that need to be combined. You have to provide linkder with path of the object files to combine.

You can then run the executable `./a.out`.

> What if we do not tell linker `foo.o` is needed? For example, if you run `gcc main.o`, you will see error `ld: symbol(s) not found for architecture x86_64` emitted by linker.

# Takeaway

* Your C program can call user-developed function whose declaration and definition stay in different folders.
* To make compilation pass, compiler needs to know include path via flag `-I`
* To make linking pass, linker needs to know the full list of object files with their correct path.

> Linking multiple objects is only one form of injecting function implementation into executable. Function could also be delivered via shared libraries or static libraries. We will cover them later.
