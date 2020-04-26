![](bash_logo.png)

# Lecture 3: Linux file system. Positional parameters. Your first Linux/Bash command. Command precedence

**Last update**: 20200426

### Table of Contents
1. [Linux file system](#file_system)
2. [Positional parameters](#positional_parameters)
3. [Your first **Linux/Bash** commands](#first_command)
4. [Command precedence](#precedence)

### 1. Linux file system <a name="file_system"></a>

We have already seen how in your current terminal session you can make your own files (e.g. with **touch**, **cat** or **nano**), and directories (with **mkdir**). However, in the Linux world files and directory placement is not arbitrary, and they need to follow some common, widely accepted, structure. The top directory is the so called _root_ directory and is denoted by ```/``` (slash). You can see its content by executing the following code snippet:
```bash
cd /
ls -al
```
All files and directories on your computer are in one of these subdirectories. Schematically, the Linux file system structure can be represented with the following diagram:

 <p align="center"> ![](linux_file_system.png) <p> 

The **Bash** built-in command **cd** ('change directory') is used to move from the current working directory to some other directory. It accepts only one argument, which is interpreted either as an absolute path to the new directory (the argument starts with ```/```), or as an relative path to new directory (relative to your current working directory). If confused where you stand at the moment in the Linux filesystem (i.e. where is your current working directory in the overall filesystem hierarchy), you can always get that information either from **Bash** built-in command **pwd** ('print working directory'):
```bash
pwd
```
or by referencing the content of environment variable **PWD** which is always set to the absolute path of your current working directory:
```bash
echo $PWD
```
Both versions return exactly the same answer. However, and as a general rule of thumb, it's always more efficient to get information directly from variable, than by retreiving the same information by executing the command via the so-called command substitution operator (more on this later).

The most important directories in the Linux file system structure are:

* ```/bin``` : most essential user command binaries (executables) used by all users;
* ```/usr/bin``` : additional user command binaries (executables)
* ```/dev``` : location of special or device files
* ```/etc ```: configuration files 
* ```/home``` : holds user-specific accounts, personal area for each user 
* ```/proc``` : kernel and process information
* ```/tmp``` : temporary files

For instance, we have already used **Linux** commands, **date** and **touch**. But to which physical executables, stored somewhere in the filesystem, these two commands corresponds to? You can figure that out by using command **which**. For instance:
```linux
which date
/bin/date
```
```linux
which touch
/usr/bin/touch
```
It is completely equivalent to execute in the terminal the command name, e.g. **date**, or the full absolute path to the corresponding executable, i.e.
```linux
date
Di 7. Mai 15:44:42 CEST 2019
```
is the same as:
```linux
/bin/date
Di 7. Mai 15:44:42 CEST 2019
```
Clearly, it would be very tedious if each time we would like to use the certain command in the terminal, we would need to specify its absolute path in the file system, both in terms of typing and in terms of memorizing the exact locations of executables in the file system. This is precisely where **Bash** (or any other shell) is extremely helpful --- shell finds and executes the right executable in the filesystem for us, after we have typed the short command name in the terminal. Clearly something is happening behind the scene, i.e. how does shell know which physical executable in the file system to link with the short command name you have typed in the terminal? Hypothetically, we could also have another version of **date** command sitting somewhere else in the filesystem, e.g. in the directory ```/usr/bin/date```. Then there is an ambiguity, as after we have typed in the terminal **date**, it is not clear whether we want ```/bin/date``` or ```/usr/bin/date``` to be executed. This is resolved with the very important environment variable **PATH**. To see its current content, simply execute:
```bash
echo $PATH
```
The output could look like this:
```Linux
/home/abilandz/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```
This output looks messy, but in fact it has a well defined structure and is easy to interpret. In the above output, we see absolute paths to few directories, separated, in this context with the field separator, ```:``` (colon). The directories specified in the **PATH** environment variable are extremely important, as only inside them **Bash** will be searching for a corresponding executable for the command name you have typed in the terminal. Literally, command **date** works in the terminal because the directory **/bin**, where its corresponding executable ```/bin/date``` sits, was added to the content of **PATH** environment variable. Order of directories in **PATH** variable also matters: When **Bash** finds your executable in some directory specified in **PATH**, it will stop searching in the other directories in **PATH**. The priority of search is from left to right. Therefore, if you have two executables in the filesystem for the same command name, e.g. ```/bin/date``` and ```/usr/bin/date```,  and if the content of **PATH** is as in the example above, then after you have typed in the terminal **date**, **Bash** would execute ```/usr/bin/date``` and not ```/bin/date```, because ```/usr/bin``` is specified before ```/bin``` in **PATH**.  Since there is no **date** in ```/usr/bin```, **Bash** continues the search for it in ```/bin```,  finally finds it there, and then executes ```/bin/date``` .  By manipulating the order of directories in **PATH** variable, you can also have your own version of all Linux commands --- just place the directory with your own executables at the beginning of **PATH** variable, and then those directories will be searched first by **Bash**. As a side remark, if you unset **PATH** variable, all commands will stop working when you type them the terminal, as **Bash** doesn't know where to look for the corresponding executables in the file system. Finally, if you want to add a new directory (e.g. ```/home/abilandz/myCommands```) holding some personal executables to **PATH**, which shall be searched with the higher priority than the already existing directories in **PATH**, redefine the **PATH** with the following standard code snippet:
```bash
PATH="/home/abilandz/myCommands:${PATH}"
```
For the lower priority, use an alternative:
```bash
PATH="${PATH}:/home/abilandz/myCommands"
```

As a final remark, the **PATH** search is skipped when the command name has ```/``` character. In this case, **Bash** assumes that you have specified the abs path to command name, and tries to execute it on the spot. This explains the standard syntax to execute straight the executable in your current directory:

```bash
./someExecutable
```

In this context, the dot ```.``` is merely an absolute path to the current directory (**ls** and **ls .** gives the same answer; the shorthand notation for the parent directory is ```..```) . With this syntax, even if **someExecutable** with a different implementation exists in same directory stored in **PATH**, it will never be searched for an executed. 

Some frequently used Linux commands to work with the file system structure are:

* **cp** : copy file(s)
```bash
cp <abs-path-to-file_1> <abs-path-to-file_2> # copying and renaming a file
cp <abs-path-to-file-1> <abs-path-to-file-2> ... <abs-path-to-some-directory> # only copying
```
When the context makes sense, **cp** can also accept the relative paths. 

* **cp -r** : copy directory and preserve its subdirectory structure
```bash
cp -r <abs-path-to-dir_1> <abs-path-to-dir_2> # this will copy <dir_1> into subdirectory of <dir_2>
```

* **rm** : delete file(s)
```bash
rm <file_1> <file_2> ... # delete the specifed (either via absolute or relative path) files
```
Use **rm** with great care, as after you have deleted the file, there is no way back!

* **rm -rf** : delete one or more directories
```bash
rm -rf <dir_1> <dir_2> ... # delete the specifed (either via absolute or relative path) directoriess
```
Use **rm -rf** even with the greatear care, as after you have deleted the directory, there is no way back!

* **mv** : move or rename the file
```bash
mv <abs-path-to-file_1> <abs-path-to-file_2> # moving, if two files are NOT in the same directory. Otherwise, just renaming a file
```
The command **mv** works in the same way with directories (no additional flags are needed, like for **cp** or **rm**).

* **du -sh** : ('disk usage') get the summary (flag **s**) of the size of directory in the human-readable (flag **h**) format 
```linux
du -sh ${HOME} # prints how much disk space your home directory is taking
967M
du -h --max-depth=1 ${HOME} # the size of directory, and differentially of its subdirectories
du -h --max-depth=2 ${HOME} # the size of directory, differentially of its subdirectories and sub-subdirectories
```

* **df -h** : ('disk free') get the status (i.e. the used disk space) of disks
```linux
df -h # in one go get the status of all disks on your computer 
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1       1.8T  1.6T  132G  93% /
```

* **stat** : displays the status of file or directory
```linux
stat Lecture_2.html 
  File: ‘Lecture_2.html’
  Size: 42263     	Blocks: 88         IO Block: 4096   regular file
Device: 801h/2049d	Inode: 11073039    Links: 1
Access: (0664/-rw-rw-r- -)  Uid: ( 1000/abilandz)   Gid: ( 1000/abilandz)
Access: 2019-05-04 12:20:13.983424152 +0200
Modify: 2019-05-03 12:08:06.499382290 +0200
Change: 2019-05-03 12:08:06.499382290 +0200
 Birth: - 
```
Later we will learn how to parse and extract from the command output only the information we need, for the time being, if you want to get only the size of the file in bytes, use:
```linux
stat -c %s Lecture_2.html
42263
```
In general, each file or directory in Linux has 3 kind of timestamps:

- **Access** --- the last time the file was read
**Modify** --- the last time the file was modified (content has been modified)
**Change** --- the last time meta data of the file was changed (e.g. permissions)

These 3 timestamps are not an overkill, in fact they enable a lot of very powerful features when searching for specific files or directories in the file system. For instance, by using them, it is possible to list names of all files modified within last day, to delete all files which were not accessed for more than 1 year, etc. (more on this later).

In addition, each file or directory in Linux has 3 levels of ownership:

- **User (u)** --- the person who created the file
**Group (g)** --- the wider group to which the person who created the file belongs to
**Other (o)** --- anybody else 

File ownership becomes extremely handy in combination with file permissions, when it's very simple to set common access rights for any group of other users. 

Finally, each file in Linux has 3 levels of permissions (access rights):

-  **read (r)** --- file can be read
**write (w)** --- file can be written to (i.e. modified)
**execute (x)** --- file is executable (i.e. program)

For instance, when you execute
```linux 
ls -al <some-file>
```
you get the following example output: 
```linux
-rw-rw-r-- 1 abilandz abilandz 42263 Mai  3 12:08 Lecture_2.html
```
It is very important to understand all entries in this output, and how to modify or set some of them. Reading from left to right:

- Column #1:
o the very first character is _file type_ : ```-``` is ordinary file, ```d``` is directory, ```l``` is soft-link, etc.
o characters 2, 3 and 4 are fields for r, w or x permissions for user (i.e. for you)
o characters 5, 6 and 7 are fields for r, w or x permissions for group (i.e. wider group of people where your account belongs to)
o characters 8, 9 and 10 are fields for r, w or x permissions for anybody else
- Column #2: Number of files (always 1 for files and 2 or more for directories)
- Column #3: The user who owns the file
- Column #4: The group of users to which the file belongs
- Column #5: The size of the file in bytes (for directories, it has another meaning, it is NOT the size of directory) 

The meaning of remaining colums is trivial. 

File permissions are changed with the command **chmod**. Few examples:
```linux
chmod o+r someFile.txt
```
After the above command was executed, others (```o```) can (```+```) read (```r```) your file named ```someFile.txt```.
```linux
chmod go-w someFile.txt
```
With this version, all group members to which your account belongs to (```g```) and all others (```o```) can NOT (```-```) modify or write to (```w```) to your file ```someFile.txt```.
```linux
chmod u+x someFile.txt
```
With this version, your file ```someFile.txt``` is declared to be an executable and only you as a user (```u```) can (```+```) execute it (```x```). Remember that only the files which are executables are taken into account by **Bash** when searching through the content of directories in **PATH** variable. Therefore, when making your own **Linux** command, two formal aspects must be always met:
1. directory containing your executable must be included in **PATH**; 
2. your executable must have ```x``` permission.

As the final example:
```linux
chmod ugo+rwx someFile.txt
```
Now everybody (you, group members and others), can read, modify or execute your file. For directories, you can change permissions in one go for all files in all subdirectories, by specifying the flag ```-R``` (recursive), i.e. by using:
```bash
chmod -R <some-options-to-change-permissions> <some-directory>
```

Before we start developing the new commands from scratch in Linux, we need to introduce one very important and fairly generic concept: _positional parametrs_ . 






### 2. Positional parameters <a name="positional_parameters"></a>

Let us now see how you can pass some arguments to your script at execution. This would then clearly allow you much more freedom and power, as nothing really needs to be hardcoded in the script body. This is achieved via the so-called _positional parameters_ (sometimes also called _script arguments_).

**Example:** We want to develop a script, let's say _favorite.sh_ which takes two arguments, the first one is interpreted as a name of collider, the second as the name of collaboration, and it shall just print someting like: 
```bash
My favorite collider is <some-collider>, and my favorite experiment is <some-experiment>.
```
The solution goes as follows. In **nano** edit the file named _favorite.sh_ with the following content:
```bash
#!/bin/bash

echo "My favorite collider is ${1}, and my favorite experiment is ${2}."

return 0
```

If you now execute this script for instance as:

```bash
source favorite.sh LHC ALICE
```

the printout looks as follows:

```bash
My favorite collider is LHC, and my favorite experiment is ALICE.
```

So how does this work? It's very simple and straightforward, there is no black magic happening here! Whatever you have typed first after ```source favorite.sh``` and before the next empty character is encountered, is being declared as the 1st positional parameter, and its value is stored in the internal variable ```${1}``` ("LHC" in the example above).  Whatever you have typed next, and before the next empty character is encountered, is being declared as the 2nd positional parameter, and its value is stored in the internal variable ```${2}``` ("ALICE" in the example above). And so on --- in this way you can pass to your script as many arguments as you wish!

Once you fetch programmatically in the body of your script the passed arguments via variables ```${1}```, ```${2}```, etc. , you can do all sort of manipulations on them, which can completely modify the behavior of your script, depending which values you have specified for them. 

Final remark on positional parameters: 

* You can programmatically fetch their total number via the special variable: ```$#```
* You can programmatically fetch them all in one go via the special variables: ```$*``` or ```$@```

This in combination with looping allows you to programmatically parse over all passed arguments (i.e. no need to hardwire somewhere in your script that you expect exactly certain number of arguments, etc.). 

It is also possible to access directly the very last positional parameter, by using the indirect reference (“value of the value”) ```!``` — the syntax for last positional parameter is : ``` ${!#}``` 

**Example**: Proof of principle --- the script _arguments.sh_ which counts and prints all arguments passed to it. 
```bash
#!/bin/bash

echo "Total number of arguments is: $#"
echo "Second argument is: ${2}"
echo "The very last argument is: ${!#}"

for pp in $*; do # pp is an arbitrary name of loop variable, here its naming convention reflects 'positional parameters'
 echo "$pp"
done

return 0
```

If you execute script for instance as: 
```bash
source arguments.sh a bbb cc
```
you should get:
```bash
Total number of arguments is: 3
Second argument is: bbb
The very last argument is: cc
a
bbb
cc
```
In this way, you can instruct your own script to behave differently if certain options (flags) or arguments are passed to it. Since this is clearly a frequently used feature, the specialized built-in **Bash** command have been developed to ease the parsing and interpretation of positional parameters (see e.g. **getopts** ('get options') command).





### 3. Your first **Linux/Bash** command <a name="first_command"></a>

As the very first and respectable version of your own command in **Linux/Bash**, which can take and interpret arguments, provide exit status, etc., we can consider  **Bash** function. 

Functions in **Bash** are very similar to scripts, however the details of their implementations differ. In addition, functions are safer to use than scripts, since they have a well defined notion of local environment. This means basically that if you have the variable with the same name in your current terminal session and in the script you are sourcing or in the function you are calling, it's much easier to prevent the clash of variables with the same name, if you use functions. In addition, usage of functions to great extent can resemble the usage of **Linux** commands, and in this sense your first function developed in **Bash** can be also treated as your first command! 

Example implementation of **Bash** function could look like:

```bash
#!/bin/bash

function Hello
{
 # Comment here briefly what this function is supposed to do, or how it shall be used. E.g.:
 ## Usage: Hello <some-name>

 echo "Hello there!"
 local NAME=${1}
 echo "Your name is: ${NAME}"

 return 0

}
```

If you have saved the above snippet in the file _functions.sh_, then in order to call your function **Hello**, please do:

```bash
source functions.sh
```

From this point onward, the definitions of all functions in the file _functions.sh_ are loaded in the computer's memory, and can be in the current terminal session used as any other command. To check this, try to execute:
```bash
Hello Alice
```

The output is:

```bash
Hello there!
Your name is: Alice
```

When compared to the script implementation, there are few differences:

* Usage of key word ```function``` (an alternative syntax exists, ```<function-name>()```, but it's really a matter of taste which one your prefer)
* Body of the function must be embeded within ```{ ... }```
* For any variable defined within the function, use the key word ```local```, to restrict its scope only to the body of the function. In this way, you will never encounter the clash between variables with the same name in the terminal and in the function, when you call the function. Defined without this key word, call to function can spoil severely the environment from which the call to the function was issued, which can have really a dire consequences...
* Programmatically, you can fetch the function name in its body implementation via built-in variable ```FUNCNAME```. For the scripts, the file name in which the script was implemented can be obtained programmatically from the built-in variable ```BASH_SOURCE```.

The rest is the same as for the scripts:

* Functions accept arguments in the same way as scripts, via ```${1}```, ```${2}```, .... variables
* You can call function within another function, but only if it was defined first -- order matters in scripting language!
* Do not forget to provide the return value at the end of the function, which sets its exit status, since for most of the time functions are executed equivalently as commands, and then the exit code clearly matters
* Typically, you implement all your functions in some file, let's say _functions.sh_, and then at the end of ```${HOME}/.bashrc``` you instert the line 

```bash 
source <abs-path-to-your-file>/functions.sh
```

Since when you launch a new terminal the file ```${HOME}/.bashrc``` is being sourced before you can type anything, the implementation of your functions from file _functions.sh_ will be automatically sourced as well, which means that your functions are ready for usage, just as **Linux** commands -- in this sense the first **Bash** function you have written can be regarded also as your own first **Linux** command!





### 4. Command precedence <a name="precedence"></a>

We have seen that your very first input in the terminal, before the empty character is encoutered, will be interpreted by **Bash** as the command name, where the command name can stand for alias, built-in **Bash** command (e.g. **echo**), **Linux** executable (e.g. **date**), etc. But what happens if we have for instance alias and **Linux** executable named in the same way? For instance:
```linux
alias date='echo "Hi!"'
```
If after this definition we type in the terminal **date**, we get:
```linux
date
Hi!
```
Therefore, the alias execution got precedence over the **Linux** executable named in the same way. 

The command precedence rules in **Bash** are well defined and strictly enforced according to the following order:
       1) aliases
       2) **Bash** keywords (**if**, **for**, etc.)
       3) **Bash** functions
       4) **Bash** built-in commands (**cd**, **type**, etc.)
       5) scripts and **Linux** executables (for which search and precedence is determined based on the content of **PATH** variable)

Given this order, clearly some care is needed when introducing new aliases or functions in **Bash**. If you have used the same name as for some already existing **Linux** executable, that executable will not be invoked when executing the alias or function with that name. If you have overwritten **Linux** executable with some alias, simply unalias that name via:
```linux
unalias <some-name>
```

In the case you are not sure to which one of the five cases above the command you intend to use corresponds to, use the **Bash** built-in command **type**:

```bash
type date
date is /bin/date
```

The above line tells that **date** is **Linux** command (or executable, or binary), and **Bash** has found its executable in the file ```/bin/date``` . Two other possibilities:

```bash
type echo
echo is a shell builtin
```

```bash
type ll
ll is aliased to `ls -alF'
```

For the **Bash** functions, **type** command also prints the source code of that function. For instance: 
```bash
type quote
quote is a function
quote () 
{ 
    local quoted=${1//\'/\'\\\'\'};
    printf "'%s'" "$quoted"
}

```





