# fork vs exec

---

What is a syscall? A syscall is a request made from application (run by the user) to the OS. There are a lot of different syscalls, for example in Linux OS there are more than 300 types of syscalls. A user program (application) is usually limited to its own address space so that it cannot access or modify other running programs or the OS itself, and is usually prevented from directly manipulating hardware devices, but many normal applications obviously need access to these components, so syscalls are made available by the OS to provide well-defined, safe implementations for such operations.

When syscall is made to the kernel then control is passed to the kernel, which determines whether the calling program should be granted the requested service. If the service is granted, the kernel executes a specific set of instructions over which the calling program has no direct control and then returns control to the calling program.

In Unix systems a syscall quite often results in invoking the `fork` and `exec` system calls.

There are different models of how threads communicated to each other during syscall. One of the mostly used model is 1-to-1 model, in this model every user thread gets attached to a distinct kernel-level thread during a system call. This model is used in all major Linux distributions, macOS, iOS, recent Windows and Solaris versions.

Before we look at `fork` and `exec` - one needs to distinguish between a process and a thread, and between process / process group / session.



## Process vs Thread

Threads run in a shared memory space, while processes run in separate memory spaces. A process has a complete, private set of basic run time resources particularly each process has its own memory space. Each process provides the resources needed to execute a program.
Threads exist within a process and every process has at least one thread. Each process provides the resources needed to execute a program. Each process is started with a single thread, known as the primary thread. A process can have multiple threads in addition to the primary thread. Changes to the primary thread may affect the behavior of the other threads of the process while changes to the parent process does not affect child processes. Processes are heavily dependent on system resources available while threads require minimal amounts of resource, so a process is considered heavyweight while a thread is lightweight.


## Process vs Process Group vs Session

Almost every time you execute a command, your shell will fork a new process(a fork under the hood almost always is followed by exec), regardless of whether you run it with & or not (& only means you're running it in the background). But note that some commands, like `cd` are shell functions and will usually not fork a new process. `type` command will usually tell you whether command is an external command or a shell function. For example `type cd` tells you that `cd` is a builtin shell function. The new forked process has membership in the original shell's "process group". If that shell or the process group gets certain signals (e.g. `SIGHUP`) then all processes running within that group get terminated. As was explained above - if you do not want your child process (i.e. a process that was created by the current shell, these processes normally belong to the same process group as the shell itself) to terminate on `SIGHUP` then you need to use `nohup` (normally done with combination of &), which will keep the program running if you just close the terminal (`SIGHUP`), but still will not prevent from CTRL+C (`SIGINT`). E.g.:

```bash
nohup firefox
```

will launch firefox browser, and if you close the terminal the browser will keep running, but if you do CTRL+C in the terminal then firefox will get closed. Command `nohup` behaves like this because it was specifically programmed to do certain things before the `exec` system call to make the forked process ignore `SIGHUP`. Note that when you run a program with `nohup` like above and also append & then the new forked process gets created in another process group, thus neither `SIGINT` nor `SIGHUP` will have any effect as the process is already in a different group altogether.

So what is a process group? It is just a unix kernel concept of managing parent and child processes. As was said earlier when a child process is created (normally under the hood with fork), it remains in the same process group as its parent, and gets terminated when parent process gets killed. There are could be cases where child processes are turned into a process group of their own as explained above with `nohup`, then they do not get killed when parent process gets killed.

What is a process? A process is a thread of execution (or collection of threads) and other context (e.g. address space, file descriptor table etc). A process may start other processes (child processes), these new processes will belong to the same process group as the parent, as was already said earlier.

The shell has the concept of "foreground" jobs and "background" jobs. Foreground jobs are process groups with control of the terminal, and background jobs are processes without control of the terminal. The background jobs might or might not belong to the same process group as the foreground jobs.


## fork(), exec() and clone()

Let's look at exec first, when you run:

    > sh -c 'command'

you run a sh instance, then start command as a child of that sh instance. When command finishes, the sh instance also finishes.

    > sh -c 'exec command'

runs a sh instance, then replaces that sh instance with the command binary, and runs that instead.
In the above example there's no difference between running the command with or without exec, but there are some situations where you want the shell
to set up the environment as a preparation for running command. This is pretty much the sole situation where exec command is useful, and should be
run directly without fork, e.g.:

    #!/bin/sh
    ENVIRONMENT=$(some complex task)
    exec command

This script does some stuff to prepare the environment so that it contains what is needed. Once that's done, the sh instance is no longer necessary, and so it's a (minor) optimization to simply replace the sh instance with the command process, rather than have sh run it as a child process. So basically what `exec` does is - it replaces the current program in the current process, without forking a new process. As you can see from the above example `exec` is normally used after the environment is created, but what if you just want to copy the environment of the parent process? Well as this is required in 90% of the cases - the `fork` system call was implemented. That is why most of the time `fork` is being used and you rarely need to use `exec`.
So fork() creates a new child process, which is a complete copy of the parent process, but child process will have it’s own unique process ID and
have it’s own address space and memory.

Now, why we mentioned threading above?

Different applications implemented differently in terms of parallel processing of tasks: some use forking (as explained above) which create new
processed and some use threading i.e. tasks are executed in parallel threads not processes. What is the difference?
Fork is universally accepted than thread because of the following reasons:

* Development is much easier on fork based implementations.
* Fork based code a more maintainable.
* Forking is much safer and more secure because each forked process runs in its own virtual address space, if one process crashes or has a buffer overrun, it does not affect any other process at all
* Threads code is much harder to debug than fork.
* Forking is faster than threading on single cpu as there are no locking over-heads or context switching

Some of the applications in which forking is used are: telnetd, Apache2, httpd, PostgreSQL. But threads also have their advantages:

* Thread are most effective on multi-processor or multi-core systems.
* For thread only one process/thread table and one scheduler is needed.
* All threads within a process share the same address space.
* Threads reduce overhead by sharing fundamental parts.
* Threads are more effective in memory management because they uses the same memory block of the parent instead of creating new.
* If properly designed and implemented threads give you more speed because there is no any process level context switching in a multi threaded application.
* Threads are really fast to start and terminate.

Some of the applications in which forking is used are: MySQL, Firebird
