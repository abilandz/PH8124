![](bash_logo.png)

# Lecture 3: Linux file system. Positional parameters. Your first Linux/Bash command. Command precedence

**Last update**: 20200428

### Table of Contents
1. [**Linux** file system](#file_system)  
	A) [File metadata](#file_metadata)     
2. [Positional parameters](#positional_parameters)
3. [Your first **Linux/Bash** commands](#first_command)
4. [Command precedence](#precedence)

### 1. **Linux** file system <a name="file_system"></a>
We have already seen how in your current terminal session you can make your own files (e.g. with **touch**, **cat** or **nano**), and your own directories (with **mkdir**). The organisation of files and directories in **Linux** is not arbitrary, and it follows some common, widely accepted, structure. The top directory is the so-called _root_ directory and is denoted by ```/``` (slash). You can see its content by executing the following code snippet in the terminal:
```bash
cd /
ls
```
The output could look like:

```bash
bin  boot  dev  etc  home  lib  media  opt  proc  root  run  sbin  sys  tmp  usr  var
```

All files and directories on your computer are in one of these subdirectories. Schematically, the **Linux** file system structure can be represented with the following diagram:

![](linux_file_system.png)


The **Bash** built-in command **cd** ('change directory') is used to move from the current working directory to some other directory. It accepts only one argument, which is interpreted either as an absolute path to the new directory (if the argument starts with ```/```), or as a relative path to the new directory (relative to your current working directory). If confused where you stand at the moment in the **Linux** file system (i.e. where is your current working directory in the overall file system hierarchy), you can always get that information either from **Bash** built-in command **pwd** ('print working directory'):
```bash
pwd
```
or by referencing the content of environment variable **PWD**, which is always set to the absolute path of your current working directory:
```bash
echo $PWD
```
Both versions return the same answer in all cases of practical interest. However, and as a general rule of thumb, it's always more efficient to get information directly from a variable, than to retrieve the same information by executing the command via the so-called _command substitution operator_ (more on this later).

The most important directories in the **Linux** file system structure are:

* ```/bin``` : essential binaries needed for normal system functioning at any run level
* ```/usr/bin``` : binaries used by all locally logged in users
* ```/usr/sbin``` : binaries which can be used only with superuser (root) privileges
* ```/dev``` : location of special or device files
* ```/etc ``` : system-wide configuration files 
* ```/home``` : holds user-specific accounts, personal area for each user 
* ```/proc``` : kernel and process information
* ```/tmp``` : temporary files

For instance, we have already used **Linux** commands **date** and **touch**. But to which physical executables, stored somewhere in the file system, these two commands correspond to? You can figure that out by using command **which**. For instance:
```bash
which date
/bin/date
```


```bash
which touch
/usr/bin/touch
```
It is completely equivalent to execute in the terminal the command name, e.g. **date**, or the full absolute path to the corresponding executable, i.e.
```bash
date
Mon Apr 27 16:12:06 CEST 2020
```
is the same as:
```bash
/bin/date
Mon Apr 27 16:12:06 CEST 2020
```
It would be very tedious and impractical if each time we would like to use some command, we would need to type in the terminal the absolute path to its executable sitting somewhere in the **Linux** file system, both in terms of typing and in terms of memorizing the exact locations of executables in the file system hierarchy. This is precisely where **Bash** (or any other **shell**) is extremely helpful --- **shell** finds and starts the correct executable in the file system for us, after we have typed the short command name in the terminal. Clearly, something is happening here behind the scene: How does **shell** know which physical executable in the file system is linked with the short command name you have typed in the terminal? Hypothetically, we could also have another version of **date** command sitting somewhere else in the file system, e.g. in the directory ```/usr/bin/date```. Then there is an ambiguity, since after we have typed in the terminal **date**, it is not clear whether we want ```/bin/date``` or ```/usr/bin/date``` to be executed. 

This is resolved with the very important environment variable **PATH**. To see its current content, simply type:

```bash
echo $PATH
```
The output could look like this:
```bash
/home/abilandz/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```
This output looks messy, but in fact it has a well-defined structure and is easy to interpret. In the above output, we can see absolute paths to few directories, which are separated in this context with the field separator ```:``` (colon). The directories specified in the environment variable **PATH** are extremely important, because only inside them **Bash** will be searching for a corresponding executable, after you have typed the short command name in the terminal. Literally, command **date** works in the terminal because the directory **/bin**, where its corresponding executable ```/bin/date``` sits, was added to the content of **PATH** variable. The order of directories in **PATH** variable also matters: When **Bash** finds your executable in some directory specified in **PATH**, it will stop searching in the other directories specified in **PATH**. The priority of the search is from left to right. Therefore, if you have two executables in the file system for the same command name, e.g. ```/bin/date``` and ```/usr/bin/date```,  and if the content of **PATH** is as in the example above, after you have typed in the terminal **date**, **Bash** would execute ```/usr/bin/date``` and not ```/bin/date```, because ```/usr/bin``` is specified before ```/bin``` in the **PATH** variable. However, since there is no **date** in ```/usr/bin```, **Bash** continues the search for it in ```/bin```,  finally finds it there, and then executes ```/bin/date``` . 

By manipulating the ordering of directories in **PATH** variable, you can also have your own version of any **Linux** command --- just place the directory with your own executables at the beginning of **PATH** variable, and then those directories will be searched first by **Bash**. For instance, you can have your own executable for **date** in your local directory for binaries (e.g. in ```/home/abilandz/bin```). Then, you need to redefine **PATH** in such a way that it has your personal directory with higher priority, when compared to standard system-wide directories for command executables (like ```/bin```, ```/usr/bin```, etc.). This is achieved with the following standard code snippet:

```bash
PATH="/home/abilandz/bin:${PATH}"
```
With this syntax, your personal executables in ```/home/abilandz/bin``` will have the highest priority in the **Bash** search. For the lower priority of your executables, use an alternative standard code snippet:

```bash
PATH="${PATH}:/home/abilandz/bin"
```

This way, you will use your own version of some standard command only if the standard, system-wide, executable is not found by **Bash**. As always, if you want to make such definitions permanent in your terminal, add the above redefinition of **PATH** into ```~/.bashrc``` file.

From the above explanation, it is clear that if you unset the **PATH** variable, all commands will stop working when you type them in the terminal, because **Bash** doesn't know where to search for the corresponding executables.

We conclude the explanation of **PATH** variable with the following important remarks:

* The search for the corresponding executable after you typed the short command name in the terminal is optimized in the following ways: 

  * Not all the files in the specified directories in **PATH** are considered during the search --- only the files which have _execute_ (or 'x') permission are taken into account (more on this in a moment!);

  * The recently used commands are _hashed_ in the table --- this table is then looked up first by **Bash** after you type the command name in the terminal. To see the current content of the hash table, just type **Bash** built-in command **hash** in the terminal. The output could look like:

    ```bash
    hits    command
       4    /usr/bin/which
       5    /usr/bin/git
       1    /bin/date
       2    /bin/cat
       6    /bin/ls
    ```

    Cleary, the hash mechanism adds it a lot to the efficiency of commands' usage in **Linux**.

* The **PATH** search can be skipped by user.  In particular, when the command name has ```/``` character, **Bash** does not perform the search for the corresponding executable --- underlying assumption is that you have now yourself specified the path in the file system, either absolute or relative, to the corresponding executable. In this case, **Bash** tries to execute that command on the spot. This explains the standard syntax to run the command whose executable is in your current directory: 

  ```bash
  ./command
  ```

  In this context, the dot ```.``` is a shortcut syntax for the absolute path to the current working directory (the analogous shorthand notation for the parent directory is ```..```). With the above syntax, even if **command** with a different implementation exists in some directory stored in **PATH**, it will never be searched for and executed. 

Some frequently used **Linux** commands to work within the file system are:

* **cp** : copy file(s)
```bash
cp <abs-path-to-file-1> <abs-path-to-file-2> # copying and renaming a file
cp <abs-path-to-file-1> <abs-path-to-file-2> ... <abs-path-to-some-directory> # copying two or more files in the same directory. The names of original files are preserved
```
When the context makes sense, **cp** also accepts the relative paths. 

* **cp -r** : copy directory and preserve its subdirectory structure
```bash
cp -r <abs-path-to-dir-1> <abs-path-to-dir-2> # this will copy the whole first directory into subdirectory of the second directory
```

* **rm** : delete file(s)
```bash
rm <file-1> <file-2> ... # delete the specifed files (it accepts both absolute and relative path to file names)
```
Use **rm** with great care, because after you deleted the file, there is no way back!

* **rm -rf** : delete one or more directories
```bash
rm -rf <dir-1> <dir-2> ... # delete the specified directories
```
Flag **-r** ('recursive') is needed to indicate that you want to delete all subdirectories recursively, **-f** ('force') is needed to avoid the prompt message which would ask you for each file separately whether you want to delete it or not. Use **rm -rf** with the greatest possible care, because after you have deleted the directory, there is no way to get back any file that was in that directory!

* **mv** : move or rename the file
```bash
mv <abs-path-to-file-1> <abs-path-to-file-2> # moving, if two files are NOT in the same directory. Otherwise, just renaming a file. Accepts also relative paths
```
The command **mv** uses the same syntax for directories (no additional flags are needed, like for **cp** or **rm**).

* **du -sh** : ('disk usage') get the summary (option **-s**) of the size of directory in the human-readable (option **-h**) format 
```bash
du -sh ${HOME} # prints how much disk space your home directory is taking
967M
du -h --max-depth=1 ${HOME} # the size of directory, and differentially of its subdirectories
du -h --max-depth=2 ${HOME} # the size of directory, differentially of its subdirectories and all sub-subdirectories
```

* **df -h** : ('disk free') get the status (i.e. the used disk space) of all disks
```bash
df -h # in one go get the status of all disks on your computer 
file system      Size  Used Avail Use% Mounted on
/dev/sda1       1.8T  1.6T  132G  93% /
```

* **stat** : displays the detailed metadata of file or directory (its size, the time it was last modified, etc.)
```bash
stat Lecture_2.md # just specify the abs. or rel. path to file as an argument
  File: Lecture_2.md
  Size: 97805           Blocks: 384        IO Block: 4096   regular file
Device: 2h/2d   Inode: 12947848928707821  Links: 1
Access: (0666/-rw-rw-rw-)  Uid: ( 1000/abilandz)   Gid: ( 1000/abilandz)
Access: 2020-04-15 21:05:26.002857000 +0200
Modify: 2020-04-28 11:44:53.454187100 +0200
Change: 2020-04-28 11:45:14.515681300 +0200
 Birth: -
```
Later we will learn how to parse through and extract programmatically from any command output (or from any physical file) only the information we need. For the time being, if you want to get only the size of the file in bytes, use:
```bash
stat -c %s Lecture_2.md
97805
```
As you can see from the output of **stat**, the example file ```Lecture_2.md``` is characterized by three timestamps: **Access**, **Modify** and **Change**. These three timestamps are an important part of file metadata, which we cover next.

#### A)  File metadata <a name="file_metadata"></a>

File metadata is any file-related information besides its regular content. From the user's perspective, the most important file metadata are _timestamps_, _ownership_ and _permissions_.  

The meaning of three timestamps is as follows:  

* **Access (a)** : last time a file was accessed (opened) and read without any modification  
* **Modify (m)** : last time a file was modified (i.e. its content has been edited)
* **Change (c)** : last time a file's metadata was changed (e.g. permissions)  

These three timestamps are not an overkill, in fact, they enable a lot of very powerful features when searching for specific files or directories in the file system. For instance, by using them, it is possible to list names of all files modified within the last day, to delete all files which were not accessed for more than 1 year, etc. (more on this later).

Next, each file or directory in **Linux** has three distinct levels of ownership:  

* **User (u)** : the person who created the file
* **Group (g)** : the wider group to which the person who created the file belongs to
* **Other (o)** : anybody else   

File ownership becomes extremely handy in combination with file permissions, when it's very simple to set common access rights for any group of other users. 

Finally, each file in **Linux** has three distinct levels of permissions (or access rights):

* **Read (r)** : file can be read
* **Write (w)** : file can be written to (i.e. edited)
* **Execute (x)** : file is executable (i.e. binary, program)

For instance, when you execute
```bash 
ls -al <some-file>
```
you can get the following example output: 
```bash
-rw-rw-rw- 1 abilandz alice 97805 Apr 28 12:23 <some-file>
```
It is very important to understand all entries in this output, and how to modify or set some of them. Reading from left to right:

- **Column #1:**  

  o the very first character is the file type : ```-``` is an ordinary file, ```d``` is directory, ```l``` is soft-link, etc.  

  o characters 2, 3 and 4 are fields for ```r```, ```w``` or ```x``` permissions for the user (i.e. for you)  

  o characters 5, 6 and 7 are fields for ```r```, ```w``` or ```x``` permissions for the group (i.e. wider group of people where your account belongs to)  

  o characters 8, 9 and 10 are fields for ```r```, ```w``` or ```x``` permissions for anybody else  

- **Column #2:** Number of files (always 1 for files and 2 or more for directories)

- **Column #3:** The user who owns the file ('abilandz' in this case)

- **Column #4:** The group of users to which the file belongs (the 'alice' experiment in this case)

- **Column #5:** The size of the file in bytes (for directories, it has another meaning, it is NOT the size of directory!) 

The meaning of the remaining columns is trivial. 

File permissions are changed with the **Linux** command **chmod**. This is best illustrated with a few concrete examples:
```bash
chmod o+r someFile.txt
```
After the above command was executed, others (```o```) can (```+```) read (```r```) your file ```someFile.txt```.
```bash
chmod go-w someFile.txt
```
In the above example, all group members to which your account belongs to (```g```) and all others (```o```) can NOT (```-```) modify or write to (```w```) to your file ```someFile.txt```. Therefore, after this simple command execution, only you can edit this file!
```bash
chmod u+x someFile.txt
```
With the above syntax, the file ```someFile.txt``` is declared to be an executable and only you as a user (```u```) can (```+```) execute it (```x```). Remember that only the files which are executables are taken into account by **Bash** when searching through the content of directories in **PATH** variable. Therefore, when making your own **Linux** command, two formal aspects must be always met:

1. the directory containing your executable must be included in **PATH**; 
2. your executable must have ```x``` permission.

Next example:
```bash
chmod ugo+rwx someFile.txt
```
Now everybody (you as a user (```u```), group members (```g```)  and others (```o```)), can read (```r```), modify or write to (```w```), or execute your file (```x```). For directories, you can change permissions in one go for all files in all subdirectories, by specifying the flag ```-R``` ('recursive'), i.e. by using schematically:
```bash
chmod -R <some-options-to-change-permissions> <some-directory>
```

Finally, we clarify that each permission setting can be represented alternatively by a numerical value. The rule is established with the following simple table:

|permission| r  | w  | x  | -  |
|:--:|:--:|:--:|:--:|:--:|
|**value**| 4  | 2  | 1  | 0  |

When these values are added together, the sum is used to set specific permissions. 

For example, if you want to set only 'read' and 'write' permissions, you need to use a value 6, because from the above table, it follows immediately: 4 ('read') + 2 ('write') = 6.

If you want to remove all  'read', 'write' and 'execute' permissions, you need to specify 0.

**Example:** Make a new file with default permissions, then remove all permissions, and set the permission pattern to ```-rwx--xr--``` , by using both syntaxes described above. With the first syntax, we would have

```bash
touch file.log # make a new file
# the default permission pattern is: -rw-rw-rw-
chmod ugo-rwx file.log # stripping off all permissions
# pattern is now: ----------
chmod u+rwx,g+x,o+r file.log
# pattern is: -rwx--xr--
```

With the alternative syntax, we proceeds as follows:

```bash
touch file.log # make a new file
# the default permission pattern is: -rw-rw-rw-
chmod 000 file.log # stripping off all permissions
# pattern is now: ----------
chmod 714 file.log
# pattern is: -rwx--xr--
```

It practice, it is not needed to remove old permissions and only then to set the new ones --- the old permission can be directly overwritten.

Before we start developing the new commands from scratch in **Linux**, we need to introduce one very important and fairly generic concept: _positional parameters_. 










### 2. Positional parameters <a name="positional_parameters"></a>

Let us now see how you can pass some arguments to your script at execution. This would then clearly allow you much more freedom and power, as nothing really needs to be hardcoded in the script body. This is achieved via the so-called _positional parameters_ (sometimes also-called _script arguments_).

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





