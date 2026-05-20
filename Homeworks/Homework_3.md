# **Bash** functions

**Last update:** 20260520-1

![](../Common_Figures/LinuxBashROOT_logos.png)

**Challenge #1**: Develop a **Bash** function named ```Mkdir``` which makes a directory and immediately goes into it. Directory names are specified as arguments, either via relative or absolute paths. If more than one directory name is supplied, all of them are made, and then it goes immediately into the very last directory.

**Challenge #2**: Develop a **Bash** function named ```Red``` which prints all supplied arguments in red color. 

**Hint #1:** Basic colors supported by **Bash** are encoded in the following escape sequences, which are somewhat cryptic and difficult to memorize:

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

In the above syntax, ```m``` terminates a special sequence of characters starting with ```\E[```, where ```\E``` by itself stands for ESCAPE character, encoded in octal notation as ```\033```, in hexadecimal as ```\x1B``` , or directly as ```\E``` or ```\e``` (beware that not all encodings are supported by all shells, so check out which encoding applies to your case). The first two entries between ```\E[``` and ```m```, e.g. ```1;31``` in the example above, determine foreground color. The 3rd entry determines the background color, where background colors are labeled in the same way as the foreground, just starting from 40 instead of 30. The particular choice 48 for the background color gives the same shade as the current color in the terminal window. Finally, the command ```tput sgr0``` restores the default settings in the terminal. 

If the command **echo** fails on your computer, an alternative solution can be achieved using **printf** which supports a slightly different syntax (e.g. **echo** automatically prints the new line at the end, while **printf** doesn't, etc.), for instance:
```bash
printf "\e[1;31m%s\e[0m\n" "some text"
```

**Challenge #3**: What do you need to do to be able to use the functions ```Red``` and ```Mkdir``` just like any other **Bash** or **Linux** command (i.e. to be able to call them only by their name from any place in the file system, each time you log in on the computer, or open a new terminal)?

 
