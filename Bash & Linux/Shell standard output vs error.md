# STDOUT vs STDERR

---

Some examples of using `STDOUT` (1) and `STDERR` (2) streams can be found below:

Here we redirect `STDERR` (2) to `log.txt`

    > find / -name core 2> log.txt

here we redirect `STDOUT` (1) to `log.txt` and `STDERR` (2) to `/dev/null`

    > find / -name core > log.txt 2> /dev/null

Note that whatever goes to `/dev/null` disappears

Also note that in the second example the ">" is implicit, explicitly it would be "1>" which stands for
redirection of `STDOUT` (1), so the same thing could be written as:

    > find / -name core 1> log.txt 2> /dev/null

Note that you can for example redirect `STDERR` (2) to `STDOUT` (1):

    > 2>&1 (note that in bash there's a shortcut for this: >&)

or redirect `STDOUT` (1) to `STDERR` (2):

    > >&2

Here, `>` redirects `STDOUT` (1) (implicit 1>) to `STDERR` (2). The `&` is used to indicate a file descriptor,
and file descriptor number 2 is `STDERR`,

Also note that order of redirection is important, check these two examples:

    > script.sh > my.log 2>&1

the above will send `STDOUT` (1) to `my.log` and then `STDERR` (2) to `STDOUT` (1), and as `my.log` jut been set to be used
as `STDOUT` then `STDERR` will end up being used for `STDERR` as well.

This is another example:

    > script.sh 2>&1 > my.log

this will send `STDERR` (2) to `STDOUT` (1) and then `STDOUT` (1) to `my.log`, so you end up with `STDERR` (2) shown on
the screen and `STDOUT` (1) written `to my.log`
