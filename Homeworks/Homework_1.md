# Homework #1: Practicing the use of ```help``` and ```man``` pages

Last update: 20190503

**Challenge #1**: With the single use of **Bash** built-in command **echo**, please try to reproduce exactly the following formatted output in the terminal, without hardwiring anywhere in the command input the empty characters:

```bash
Welcome
to
the
lecture on
          Linux
               Bash
                   and
                      ROOT!
```

**Hint:** In order for **echo** command to be able to interpret some backslash-escaped characters which have special meaning, you need to use option **-e**. For instance, **\n** indicates the start of new line, but it will be interpreted that way only in combination with **-e** flag.  Consider the following examples:

```bash
echo "test1\ntest2"
test1\ntest2
``` 
and 
```bash
echo -e "test1\ntest2"
test1
test2
```
And so on!

Please send the solution via email to: ```ante.bilandzic@tum.de``` 