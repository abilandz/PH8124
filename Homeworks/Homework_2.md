# Homework #2: Using external executable as Linux/Bash command

Last update: 20190510

**Challenge #1**: As a starting point of this homework, please start **nano** editor in the terminal and write the following simple code snippet of C++ programming language (as an example choice, but this homework is fairly generic, and you can use some other programming language that you prefer more) into the file ```Hello.C``` 

```c
#include <stdio.h>
int main()
{
  printf("\n Hello external world!\n\n");

  return 0;

}
```

Then, compile the above code, in order to create an executable (or binary) file. For instance, you can compile C++ code by using ```gcc``` compiler:

```linux
gcc Hello.C -o hi
```
The name which follows immediately after the flag **-o**, the **gcc** compiler interprets as the name of the final compiled executable. The remaining argument is the source code which needs to be compiled (in this case, the file ```Hello.C```).

If the compilation succeded, you shall see the new file ``hi`` in your current directory, something like:
```linux
ls -al
-rwxrwxr-x 1 abilandz abilandz    8559 Mai 10 16:00 hi
``` 

The question now is, how to use this new executable ``hi``, created with an external C++ programming language, as any other **Linux** command? If you type in the terminal only the name of executable, you get the error:
```bash
hi
hi: command not found
```
Temporarily, you can resolve this problem, by specifying explicitely where in the file system your executable sits, e.g. 
```linux
$PWD/hi
```
or using the shortcut version
```
./hi
```
But above two temporary solutions are very limited in scope, in a sense that they will work only if you execute them exactly from the same directory where the executable **hi** physically sits. And you definitely want to be able to call your executable from any place in the file system hierarchy!

**Please answer the following questions:**
1. Which environment variable do you need to modify and how, in order to fix the above error message ```hi: command not found```?
2. What do you need to do in order to be able to use **hi** straight as any other **Linux** command in any new terminal you open?
3. Which command do you need to execute in order to prevent everybody except you to run **hi** executable?


