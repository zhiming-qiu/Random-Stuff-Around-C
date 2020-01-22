# Call CLib function

We have [a minimal C program](../a-minimal-c-program/main.c) which does nothing but returns a status code. Now we want to beef it up by calling a standard C library function `printf` to display a message in console output.

The [code](./main.c) is as below:

```C
#include <stdio.h>

int main() {
    printf("Hello, world!\n");
    return 0;
}
```

# Try it out

1. Compile. Run `gcc -c main.c` to generate object file `main.o`.
2. Link. Run `gcc main.o` to generate executable file `a.out`.
3. Run. Run via `./a.out` to see message `Hello, world!` printed on console.

# C preprocessor

C function `printf` is declared in standard C header `stdio.h`. Since we are going to call it, we need to include its declaration in `main.c` and hence have the `#include` statement.

There are two questions to answer:
1. Where is the file `stdio.h`?
2. How does the file affect compilation of `main.c`?

## How to find `stdio.h`

Depending on the configuration of individual system, `stdio.h` may exist in multiple locations. GCC follows a rule to search in order to find the file. The search order can be fetched by running `gcc -Wp,-v -x c++ - -fsyntax-only`. The above command returns a list of sites for GCC to search `stdio.h`.

>On my computer, the location of the header file is `/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1`

GCC iterates through the search list and exits when it finds `stdio.h`.

## How `stdio.h` is used in compilation

When compiling a source file, GCC calls preprocessor to process source before generating object file. One thing the preprocessor does is grab the found header file (in our case, `stdio.h`) and insert its content into `main.c`.

To check the output of preprocessor, run `gcc -E main.c`. In its output, you will see the first part is `stdio.h`'s content while the second part is `main.c`'s content. The output of preprocessor will be dispatched to compiler for generating object file.

> What if we do not include `stdio.h` in `main.c`? In that case, the compiler would not find declaration of `printf` and hence issue compilation error.

# Compile

As mentioned above, the output from preprocessor will be used by compiler to generate object file `main.o`.

# Link

If you are curious, you will notice the header file `stdio.h` only declares function `printf`. It does not specify how to print message onto console. Hence, object file `main.o` does **NOT** have the actual implementation logic of `printf`. And that is part of the reason why object file cannot execute.

> If you compare the size of `main.o` and `a.out`, you will find `a.out` is way larger. In my case, `main.o` bears size 776 byte while `a.out` 8432 byte.

> High level explanation on linker can be found here [linker](https://en.wikipedia.org/wiki/Linker_(computing))

When linking the object(s), GCC will determine where to find the C standard library implementation. You can find the location of the c standard library implementation by adding flag `-v` to `gcc`:

Run `gcc -v main.o` and you will see output below:

```
Apple clang version 11.0.0 (clang-1100.0.33.17)
Target: x86_64-apple-darwin18.6.0
Thread model: posix
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin
 "/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ld" -demangle -lto_library /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/libLTO.dylib -dynamic -arch x86_64 -macosx_version_min 10.14.0 -syslibroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk -o a.out main.o -L/usr/local/lib -lSystem /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/clang/11.0.0/lib/darwin/libclang_rt.osx.a
```

You can see the library implementation is provided by `/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/clang/11.0.0/lib/darwin/libclang_rt.osx.a`.

Linker will combine the object file `main.o` and the implementation of `printf` into executable `a.out` and hence we notice the size expands after linking.

Linker performs another important task as [relocation](https://en.wikipedia.org/wiki/Linker_(computing)#Relocation). We will cover it later.

# Takeaway

When calling a standard C function, both compile and link stages will be affected. Compiler needs to find function declaration from pre-determined path while linker needs to find function implementation from pre-determined path as well.