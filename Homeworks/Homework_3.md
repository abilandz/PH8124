![](bash_logo.png)

# **Bash** functions

**Last update:** 20230526

**Challenge #1**: Develop a **Bash** function named ```Mkdir``` which makes a directory and immediately goes into it. Directory names are specified as arguments, either via relative or absolute paths. If more than one directory name is supplied, it goes immediately into the last directory.

**Challenge #2**: Develop a **Bash** function named ```Red``` which prints all supplied arguments in red color. 

**Hint #1:** Basic colors supported by **Bash** are encoded in the following escape sequences, which are rather cryptic and difficult to memorize:

```bash
Black        0;30     Dark Gray     1;30
Red          0;31     Light Red     1;31
Green        0;32     Light Green   1;32
Brown/Orange 0;33     Yellow        1;33
Blue         0;34     Light Blue    1;34
Purple       0;35     Light Purple  1;35
Cyan         0;36     Light Cyan    1;36
Light Gray   0;37     White         1;37
```

For instance, to print "some text" in red in the terminal, execute:

```bash
echo -e "\E[1;31;48msome text"; tput sgr0  
```

In the above syntax, "m" terminates special sequence of characters started with "\E[". The first two entries between "\E[" and "m", e.g. "1;31" in the example above, determine foreground color. The 3rd entry determines the background color, where background colors are labeled in the same way as foreground, just starting from 40 instead from 30. The particular choice "48" for background color gives the same shade as the terminal window. Finally, command ```tput sgr0``` restores the default settings in the terminal.

**Challenge #3**: What do you need to do to be able to use the functions ```Red``` and ```Mkdir``` just like any other **Bash** or **Linux** command (i.e. to be able to call them only by their name from any place in the file system, each time you log in on the computer, or open a new terminal)?

 