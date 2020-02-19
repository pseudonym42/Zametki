# Bash kill commands

User can manually terminate a process by running a kill command. There are a number of kill commands available and the default one is `SIGTERM` (signal terminate). `SIGTERM` (15) signal allows the process to perform any cleanup operations before shutting down.

To specify which kill signal to send use `-s` option followed by the name (or number) of the signal; to see the available options run:

```bash
kill -l
```

Let's for example kill a process by sending the required signal. e.g below is the hardest kill (`SIGKILL` (9)):

```bash
kill -s 9 <pid>
```
One of the softest kills is `SIGHUP` (1). Most daemons are programmed to actually re-read their configuration when they receive such a signal rather than terminate.

When a terminal is closed, the kernel sends `SIGHUP` to the controlling process in that terminal (i.e. the shell). The shell in turn sends `SIGHUP` to all the jobs running in the background. Running a job with `nohup` prevents it from being killed in this way if the terminal dies (which happens e.g. if you were logged in remotely and the connection drops, or if you close your terminal emulator). So whenever you you need to run a process which needs to ignore `SIGHUP`, use `nohup` command:
```bash
nohup <mycommand>
```

Above command runs command `mycommand` with `nohup` i.e. immune to `kill -s 1 <pid>`.

Also note that with `nohup` command all output, including any error messages, will be written to the file `nohup.out` in the working directory, or in your home directory. So if `mycommand` is running when you log out or close the terminal, `mycommand` will not (unlike commands started with `exec` command) terminate.

Another popular kill command is `SIGINT` (2). This is the same as starting some program and pressing `CTRL+C` during execution. Most programs will stop, you could lose data.
