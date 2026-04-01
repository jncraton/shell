Shell
=====

A minimal shell for x64 Linux systems.

## Learning Outcomes

After completing this experience, learners will be able to:

1. Identify the role of a shell in a POSIX system
2. Implement basic process control using `fork`, `execve`, and `wait4`
3. Execute system calls directly without using a standard library
4. Implement shell builtins for process termination and directory navigation

What is a shell?
----------------

At its most basic level, a shell is simply a program that exposes the operating system to a user. Shells can be graphical or they can use a command-line interface. A basic shell should allow a user to launch programs on the system.

A simple CLI shell may operate as a [read-eval-print loop](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop). It accepts input from a user, evaluates it, then displays any results of the evalation. In order to implement a REPL, we need a loop and the `read` and `write` system calls.

Implementation
--------------

This implementation strives to be as simple as possible to understand from top to bottom. It can be challenging to understand where user applications end and the kernel begins, especially with middleware such as libc present. This shell therefore includes no external libraries, including libc. A limited number of x64 Linux system calls are provided via `syscall.h`. These are:

- [read](https://man7.org/linux/man-pages/man2/read.2.html) (0)
- [write](https://man7.org/linux/man-pages/man2/write.2.html) (1)
- [fork](https://man7.org/linux/man-pages/man2/fork.2.html) (57)
- [execve](https://man7.org/linux/man-pages/man2/execve.2.html) (59)
- [exit](https://man7.org/linux/man-pages/man2/_exit.2.html) (60)
- [wait4](https://man7.org/linux/man-pages/man2/wait4.2.html) (61)
- [chdir](https://man7.org/linux/man-pages/man2/chdir.2.html) (80)

It turns out that using just these seven system calls, we can implement a basic shell.

Running Programs
----------------

In order to run additional programs on POSIX systems, we need to perform two system calls:

1. `fork` - Split the current process (our shell) into two processes
2. `execve` - Replace one process (the child) with another program

The parent shell should then loop waiting for the child to finish execution. This can be implemented using the `wait4` system call.

Builtin Functions
-----------------

In order for our shell to be able to terminate, we need a builtin to close the program. We'll use `exit` for this purpose. The `exit` builtin should call the `exit` system call to terminate the shell.

It is also helpful to be able to change the working directory that child processes will use. We can change the working directory using the `cd` builtin. This should call the `chdir` system call to change the working directory of the shell.

Task
----

You should complete the shell program by making the noted modifications to `shell.c`. You may not `#include` any libraries to complete this program. Necessary changes are indicated with `TODO` comments.

Once complete, you should be able to run your shell on an x64 Linux system and perform output like the following:

```
> cd /
> /bin/pwd
/
> /bin/echo Hello, world!
Hello, world!
> exit
```

You should even be able to run interactive programs such as `/usr/bin/vi`.

Note that this simple shell implementation does not support the `PATH` environment variable, so programs like `ls` need to be executed as `/bin/ls` using their absolute path.
