# How to use the so called here-document redirection operator <<MARKER?

This operator allows to send multi-line `STDIN` to some command. To indicate the beginning of multi-line `STDIN` and the ending of it we can use MARKER, the general practice is to use `EOL` as a MARKER. E.g.:

```bash
grep 'b' <<EOL
> fart
> fort
> bart
> EOL
```

The above command will get all three lines of `STDIN` i.e. fart, fort and bart, and will pass them to grep command. The grep command gets us the fields which contain 'b' char, so the output of the above command will be - bart.
