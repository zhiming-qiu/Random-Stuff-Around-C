# Call user function II

[call-user-function-I](../call-user-function-I/README.md) demonstrates how to link the implementation of a user defined function as an object file. In this section, we are going to cover how to link implementation of user defined function as shared object (`.so`).

> Shared object differs a plain object file in a way that when linker links it to create an executable (process image), the content of the shared object will **NOT** be copied into the executable. Instead, the executable maintains information such that wnen resource in shared object is needed at runtime, the shared object will be loaded on the fly. 

> Of course, the shared object should be found at runtime. This is different when linking a plain object file to create an executable which keeps copy of the object file and hence bears large size.

Now we are going to create a shared object for `foo.c` and test how to generate executable by linking the shared object and object file of `main.c`.

## Create shared object

### Compile foo.c

```
gcc -c -fpic -I"`pwd`/include" src/foo.c
```

You will see `foo.o` generated successfully. 
> Note the flag `-fpic`. Without this flag, the object file generated cannot be used to create shared object.

### Create foo.so

```
gcc -shared -o lib/libfoo.so foo.o
```

Notice `libfoo.so` is generated, which contains implementation of function `foo`. We name it with prefix `lib` so that when linking it we can use flag `-lfoo` which will append prefix `lib` to resolve file path.

After this step, folder `lib` has the shared object.

## Create executable

### Compile main.c

Straightforwardly, run `gcc -c -I"`pwd`/include" main.c` to create `main.o`.

### Link libfoo.so and main.o

Sounds like it should be an easy job: `gcc main.o -lfoo`. However we get error:
```
ld: library not found for -lfoo
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```
The reason is linker could not find  where `libfoo.so` is. To fix this, we need to use flag `-L` to tell linker where to search the shared object:
```
gcc -L"`pwd`/lib" main.o -lfoo
```

Fow now, we have successfully linked the shared object of `foo` with `main.o` to create executable `a.out`.

If we run `./a.out`, we should expect the program executes successfully and prints message `Foo` on console.

But wait a second :trollface:.

Now the `libfoo.so` is in folder `lib`, which is determined in build time. In production environment, the `so` file may be moved into a different folder, eg `so`. Now lets move the file from folder `lib` to `so`:
```
mkdir so
mv lib/libfoo.so so
```

If we try to run `./a.out` again, we will see an error:
```
dyld: Library not loaded: lib/libfoo.so
  Referenced from: /Users/zqiu/temp/Random-Stuff-Around-C/call-user-function-II/./a.out
  Reason: image not found
Abort trap: 6
```

When running the program, the loader could not find the shared object - at link time the location of the shared object file is in folder `lib` but now the shared object file has moved in production environment which either has no folder `lib` or has the foler without the shared object.

How to resolve?

On Linux use environment variable `LD_LIBRARY_PATH`. On Mac, use `DYLD_LIBRARY_PATH` instead.

```
export DYLD_LIBRARY_PATH=$DYLD_LIBRARY_PATH:./so
./a.out
```

With the environment variable set up properly, loader knows where to search for shared objects to resolve unknown symbols :-). This time the program should run successfully:
```
zqiu02vl:call-user-function-II zqiu$ ./a.out
Foo
```

> Since the executable does not contain implementation of `foo`, in theory, it should be smaller than its counterpart in [call-user-function-I](../call-user-function-I).

## Recap

1. Shared object is built on top of plain object file compiled with `-fpic` option with GCC
2. Linking shared object needs flag `-L` to tell linker where to find shared object
3. Executing a program linked with shared object needs to have path `LD_LIBRARY_PATH` or `DYLD_LIBRARY_PATH` set up to point to the location of shared object so loader can start the process.







