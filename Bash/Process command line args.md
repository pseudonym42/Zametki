
Bash script that checks if the arguments entered in command line are folder names and if yes prints them out.

* `-ne` in the if statements means "not equal"
* `[ -d $1 ]` checks if the first argument is folder
* `$#` contains number of arguments
* `$0` is the argument containing the name of the script

```bash
#!/bin/bash
function show_usage {
    echo "Usage: $0 source_dir dest_dir"
    exit 1
}

# Main program starts here
if [ $# -ne 2 ]; then
    show_usage
else # There are two arguments
    if [ -d $1 ]; then
        source_dir=$1
    else
        echo 'Invalid source directory'
        show_usage
    fi
    if [ -d $2 ]; then
        dest_dir=$2
    else
        echo 'Invalid destination directory'
        show_usage
    fi
fi
printf "Source directory is ${source_dir}\n"
printf "Destination directory is ${dest_dir}\n"
```
