# Homework #2: Using external executable as Linux/Bash command

Last update: 20200504

As a starting point of this homework, please start the **nano** editor in the terminal and write the following simple code snippet of C/C++ programming language (this is just an example choice --- this homework is fairly generic, and you can use some other programming language that you prefer more!) into the file ```Hello.C``` 

```c
#include <stdio.h>
int main()
{
 printf("\n Hello from C/C++ executable!\n\n");
 return 0;
}
```

Then, compile the above code, in order to create an executable (or binary) file. For instance, you can compile C/C++ code by using the widely used and freely available **gcc** compiler, with the following generic syntax:

```bash
gcc <input-source-code> -o <final-executable>
```

In your case:

```bash
gcc Hello.C -o hello
```
The name which follows immediately after the flag **-o** the **gcc** compiler interprets as the name of the final compiled executable. If you omit this part in the command input, the default name of executable is rather plain: ```a.out``` .

If the compilation went through, you will find the new file ``hello`` in your current directory, something like:
```bash
-rwxrwxrwx 1 abilandz abilandz 8392 May  4 11:32 hello
```

By default after the **gcc** compilation, you, your group members and everybody else got all permissions (```r```, ```w```, and ```x```) for the file ```hello```. 

**Challenge #1**: Which command do you need to execute to remove the write (```w```) permission both for your group and everybody else, on the file ```hello``` (this is a very simple safety measure, as after this nobody except you will be able to overwrite this executable, accidentally or not). Which command do you need to execute in the terminal to remove the execute (```x```) permission on the file ```hello``` for everybody, expect for you and your group members? Which command do you need to execute to remove the read and write permissions on the source code ```Hello.C``` for everybody except you (this way, nobody except you can see and edit the details of implementation which eventually led to the executable ```hello```)? 

**Challenge #2:** If you type in the terminal **hello** you get an error message, something like:

```bash
hello
hello: command not found
```
If you are in the same directory where ```hello``` sits, you can circumvent this by using ```./hello``` instead. If you are outside of that directory, you have to prepend the absolute path to that directory before the executable name ```hello```, which is very tedious and inconvenient.

What do you need to do to fix this problem permanently, i.e. you want to be able to use the external C/C++ executable in the terminal only by its name **hello**, at any point in the file system, each time you login, and each time you open a new terminal --- just like any other **Linux** or **Bash** command?