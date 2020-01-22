# A Minimal C Program

## Try it first

1. Generate executable from source
    - Open terminal
    - Go to folder containing source file [main.c](./main.c)
    - Generate executable via running `gcc main.c`. Notice a new file `a.out` appears.
2. Run executable
    - Run executable via `./a.out`. You should see nothing from console output.
    - Check process exit status via `echo $?`. You should see output as `0`.


## Under the hood

1. Compile
    - Compile is about converting a single source file (a compilation unit) into an object file. Object file can NOT be executed.
    - `gcc main.c` does both compile and link, and hence generates final executable.
    - If you just want to perform compile step, run `gcc -c main.c` and get the object file `main.o`.
2. Link
    - Link is about assembling object files and libraries together to generate an executable.
    - If you perform a separate compile step as above and have a `main.o` file, you can link it via `gcc main.o` and get the executable `a.out`
3. Run
    - With executable `a.out` ready, you want to run it by asking OS to load it for executiion
    - A new process will be created for the execution under the current shell environment. In our case, the process is very short lived.
    - If the process terminates normally, the exit status should be `0`, corrresponding to the statement `return 0` in [main.c](./main.c#L2).
    - You can check progress exit status via special variable `$?`. Run `echo $?` to display the status of last process (the `a.out` process).


That's it!    