# Input and Output in C


## scanf and printf functions
Both defined in `stdio.h`. Here is how you can use them:

```c
#include <stdio.h>

void main() {
    int i;
    scanf("%d\n", &i);
    printf("You entered: %d", i);
}
```

Common formats:
* `%d` - Scan or print an integer as signed decimal number
* `%f` - Scan or print a floating point number
* `%c` - Scan or print a character
* `%s` - Scan or print a character string

## getchar and putchar functions

Both defined in `stdio.h`

The `getchar` function reads a character from the terminal (standard input) and returns it as an integer. This function reads only single character at a time. You can use this method in a loop in case you want to read more than one character.

The `putchar` function displays the character passed to it on the screen and returns the same character. This function displays only a single character at a time. In case you want to display more than one characters, use `putchar` method in a loop.

```c
#include <stdio.h>

void main() {
    int c;
    c = getchar();
    putchar(c);
}
```

Above code expects user to enter a value, once entered it will display the value.


## getc and putc functions

Both defined in `stdio.h`

The `getc` function reads a character and returns it as an integer - it is very similar to `getchar`, the main difference is that it reads from a stream. The syntax is as follows:

```c
int getc(FILE *stream);
```

For example:

```c
#include <stdio.h>

int main(void) {
    printf("%c", getc(stdin));
    return(0);
}
```
Above could be written using `getchar` like this:

```c
#include <stdio.h>

void main() {
    printf("%c", getchar());
}
```

All of the above also applies to `putc`. Here's a more real world example of how these functions can be used:

```c
#include <stdio.h>

int main() {
    FILE * fp;
    char ch;
    fp = fopen("one.txt", "w");
    printf("Enter data...");
    while( (ch = getchar()) != EOF) {
        putc(ch, fp);
    }
    fclose(fp);
    fp = fopen("one.txt", "r");

    while( (ch = getc(fp)) != EOF)
        printf("%c",ch);

    fclose(fp);
    return 0;
}

```


## gets and puts functions
The `gets` function reads a line from STDIN (standard input) into the buffer pointed to by `str` pointer, until either a terminating newline or EOF (end of file) occurs. The `puts` function writes the string `str` and a trailing newline to STDOUT.

```c
#include <stdio.h>

void main() {
    char str[100];
    printf("Enter a string");
    gets(str);
    puts(str);
    getch();
}
```
Above code expects user to enter a string.

## scanf vs gets
The main difference between these two functions is that `scanf` stops reading characters when it encounters a space, but `gets` reads space as character too.

If you enter name as "Joe Bloggs" using `scanf` it will only read and store "Joe" and will leave the part after space. But `gets` function will read it completely.

For more info see https://www.studytonight.com/c/c-input-output-function.php
