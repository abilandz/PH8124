![](../Common_Figures/LinuxBashROOT_logos.png)

# Using external executable as Linux/Bash command
**Last update:** 20250515

As a starting point for this homework, start the **nano** editor in the terminal and write the following simple code snippet of C/C++ programming language (this is just an example choice &mdash; this homework is fairly generic, and you can instead use some other programming language that you prefer more) into the file ```Hello.C``` 

```c
#include <stdio.h>
int main()
{
 printf("\n Hello from C/C++ executable!\n\n");
 return 0;
}
```

Then, compile the above code in order to create an executable (or binary) file. For instance, you can compile C/C++ code by using the freely available **gcc** compiler, with the following generic syntax:

```bash
gcc inputSourceCode -o finalExecutable
```

In this particular case, you could compile with something like:

```bash
gcc Hello.C -o hello
```
The name that follows immediately after the flag **-o** the **gcc** compiler interprets as the name of the final compiled executable. If you omit this part in the command input, the default name of the executable is rather terse: ```a.out``` . This is an example of the general case where an option and the corresponding option argument come in pairs.

If the compilation went through, you will find a new file ``hello`` in your current directory, something like:
```bash
-rwxrwxrwx 1 abilandz abilandz 8392 May  4 11:32 hello
```

Given the default settings on the computer I have used, after the **gcc** compilation, you, your group members and everybody else have all permissions (```r```, ```w```, and ```x```) set for the file ```hello```, but this default behavior can change from one computer to another.  

**Challenge #1**: Which command do you need to execute to remove write (```w```) permission both for your group and everybody else, on the file ```hello``` (this is a very simple safety measure, because after this nobody except you will be able to overwrite this executable, accidentally or not). Which command do you need to execute in the terminal to remove the execute (```x```) permission on the file ```hello``` for everybody, except for you and your group members? Which command do you need to execute to remove the read and write permissions on the source code ```Hello.C``` for everybody except you (this way, nobody except you can see and edit the details of implementation, which eventually leads to the executable ```hello```)? 

**Challenge #2:** If you type in the terminal **hello** you get an error message, something like:

```bash
$ hello
hello: command not found
```
If you are in the same directory where ```hello``` sits, you can circumvent this by using  ```./hello``` instead. If you are outside that directory, you have to prepend the absolute path to that directory before the executable's name ```hello```, which is tedious and inconvenient.

What do you need to do to fix this problem permanently, i.e., you want to be able to use the external C/C++ executable in the terminal only by its name **hello**, at any place in the file system, each time you login, and each time you open a new terminal &mdash; just like any other **Linux** or **Bash** command?
