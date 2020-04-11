# How to iterate over a file?

---

Iterate through a file and print line by line:

```bash
echo "Enter filename: "
read FILE

while read -r ALINE; do
    echo "File line: $ALINE"
done < "$FILE"
```

Now, iterate through a file and print line by line and also append record to the file.

Below you can see how to use file descriptors: we can use any number except 0, 1, 2 as they are reserved for `STDIN`, `STDOUT` and `STDERR`

We'll use number 5 below, note that `<` is for reading, `>` is for writing
```bash
echo "Enter filename: "
read FILE
exec 5<>$FILE

while read -r ALINE; do
    echo "File line: $ALINE"
done <&5

# now add a record to the same file
echo "File was read on: `date`" >&5
```
