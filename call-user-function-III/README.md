# Call user function III

We demonstrate how to call user defined function delivered as shared object in [call-user-function-II](../call-user-function-II/README.md). Now we are about to cover the last topic: how to call user defined function delivered as static library (`.a`).

> :information_source: A static library is actually just a collection of plain object files (`.o`). Technically speaking, link wise there is no difference between linking against an object file and linking against a static library.

## Create static library

### Compile foo.c

```
gcc -c -I"`pwd`/include" src/foo.c
```

You will see `foo.o` generated successfully.

### Create libfoo.a

```
mkdir lib
ar rcs lib/libfoo.a foo.o
```

You can see static library `libfoo.a` is created in folder `lib`. 

## Create executable

### Compile main.c

Straightforwardly, run `gcc -c -I"`pwd`/include" main.c` to create `main.o`.

### Link libfoo.a and main.o

Run command below:

```
gcc -L"`pwd`/lib" main.o -lfoo
```

Note that option `-L` tells linker where the static library `libfoo.a` is and option `-l` tells linker static library `libfoo.a` will be linked.

Executable `a.out` will be generated. Since it is static linking, the executable does not need any runtime dependency since the implementation is already part of the executable.

## Recap

1. A static library `.a` is just a collection of `.o` files.
2. Upon successful linking, the generated executable should have the function implementation inplace and hence does not need any runtime dependency.







