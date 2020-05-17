![](bash_logo.png)


# Lecture 5: Command substitution. Input/Output (I/O). Conditional statements

**Last update**: 20200517


### Table of Contents
1. [Command substitution: **$( ... )**](#command_substitution)
2. [Input/Output (I/O) and redirections](#io)
3. [Code blocks: **{ ... }**](#code_blocks)
4. [Conditional statements](#conditional_statements)




### 1. Command substitution: $( ... ) <a name="command_substitution"></a>
We have already seen that a value can be stored in a variable by explicit assignment (using the operator ```=```),  or of the user supplies variables as command line arguments (positional parameters) to a script or a function. In practice, however, one frequently wants to store the output of some command directly into the variable, or even the content of an external file. This can be achieved with the so-called _command substitution operator_ ```$( ... )```.  For instance, we have already seen that the file size in bytes can be printed with the following:

```bash
stat -c %s someFile.log
```
But how can we fetch the above printout programmatically, and do some manipulation with it later in our code? This is precisely the case when we need to use the command substitution operator:
```bash
FileSize=$(stat -c %s someFile.log)
```
Now the size of file ```someFile.log``` is stored directly in the variable **FileSize** and from this point onwards we can reference content of that variable in the same way as the content of any other variable:
```bash
echo ${FileSize}
```

The operator ```$( ... )``` can do much more than that. For instance, it can literally in-line the output of any command at the place where this operator was used. 

**Example 1**: How to produce the following single-line output, with the current timestamp embedded:

```bash
Today is Mo 20. Mai 15:33:07 CEST 2019 . What a nice day...
```
This can be achieved with:
```bash
echo "Today is $(date) . What a nice day..."
```
The command substitution operator literally in-lined the output of **date** command at the place where it was used. This way, we can very elegantly achieve the desired more complex functionality by combining in the very same command input multiple commands, which otherwise we would need to execute one-by-one. 

Command substitution operator ``` $( ... ) ``` is a very neat construct, and it is used frequently. One classical use case is to avoid hardwiring any specific information in your code, since it can change from one computer to another.

**Example 2**: You are working in parallel on two computers, which do not have the same version of the command that you use in your code. You would like to use if possible all the latest functionalities of that command, but if that's not available, you would still like to run your code with the older version of that command. Can you make the code transparent to such a difference? You can do it schematically as follows:

```bash
Version=$(commandName -v) # flag '-v' typically prints the version
[[ $Version -lt someTreshold ]] && use-older-functionalities
[[ $Version -ge someTreshold ]] && use-newer-functionalities
```
This is just a schematic solution --- most likely the output of **commandName -v** will have some additional information that you need to filter out,  but all that can be still done within the command substitution operator.

You can fearlessly nest the command substitution operators, like in the following example. 

**Example 3**: How can you get programmatically only the name of the parent directory of the directory in which your script sits (knowing that the environment variable **PWD** holds the full absolute path of script's directory)? 

To solve this problem, we need first to introduce two widely used **Linux** commands in this context: **basename** and **dirname**. The command **basename** is typically used in the following way: It takes as an argument the absolute path to some directory or file, and drops the part which corresponds to an absolute path. This is illustrated with the following code snippets:
```bash
DirectoryPath=/home/abilandz/Lecture/PH8124/Lecture_5
basename ${DirectoryPath}
Lecture_5 # only the directory name is printed
```
On the other hand, the command **dirname** does the opposite: It prints only the absolute path to the specified directory or file. If we reuse the above example: 
```bash
DirectoryPath=/home/abilandz/Lecture/PH8124/Lecture_5
dirname ${DirectoryPath} 
/home/abilandz/Lecture/PH8124 # only the abs. path is printed
```
The commands **basename** and **dirname** can be used in exactly the same way for files.

Therefore, the solution to our initial problem can be fairly elegant and concise, if we use these two commands in combination with command substitution operator:
```bash
DirectoryPath=/home/abilandz/Lecture/PH8124/Lecture_5
ParentDirectoryName=$(basename $(dirname $DirectoryPath))
echo $ParentDirectoryName
PH8124 # only the parent directory name is printed
```
We can use multiple commands within the same command substitution operator, they just need to be separated with delimiter ```;```, as in the following example:
```bash
Var=$(date;pwd)
echo "$Var"
```
The printout is

```
Thu May 14 11:58:28 CEST 2020
/home/abilandz/Lecture
```

It is perfectly fine to inline the output of your function with this operator:

```bash
echo "Output of my function is: $(someFunction) . Very nice!" 
```

or to store the printout of a function in the variable:

```bash
Var=$(someFunction)
```

Finally, the very neat use case of the command substitution operator is to store the content of some external file in the variable. The relevant syntax is:

```bash
FileContent=$(< someFile) 
```
In the above example, ```<``` is just a shortcut for the command **cat**, which can be used completely equivalently in this context:
```bash
FileContent=$(cat someFile) 
```


This great functionality circumvents the necessity of dealing with too many temporary files during the code execution, when we are interested to keep the file content only at a particular time. With the above definitions, the following two commands yield exactly the same answer initially:

```bash
cat someFile # reads the content of a physical file
echo "${FileContent}" # references the content of variable
```
However, if the content of the physical file ```someFile``` has changed or if it was deleted, that does not affect the value of variable **FileContent**. This is very handy when we need to initialize our script or function with the content of some external file: if we store that information in the variable, we have removed completely the dependency of our code on that external file.

The command substitution operator is frequently used in combination with the **for** loop, when we want to iterate over all elements in the output of some command. Also in this context distinct elements of the list are separated with one or more empty characters. This is best illustrated with the following example:

**Example 4**: How can we loop over all files in the current directory and print the size of each file?

One example solution is provided with the following code snippet:
```bash
for File in $(ls $PWD); do
 [[ -f $File ]] && Size=$(stat -c %s $File) || continue
 echo "The size of ${File} is: ${Size}" 
done
```
Note that if you would have used the lengthy output of **ls** by specifying the flag **-l**, then the loop variable **File** would loop over all entries in the command output separated with one or more empty characters, therefore also over the permission field, user name, etc. This is illustrated in the following example:

```bash
for Var in $(date); do
 echo $Var
done
```
The output is:
```bash
Thu
May
14
13:13:50
CEST
2020
```
This is another example to illustrate the importance of empty character as being the default field separator in **Linux/Bash**.

In the end, we would like to remark that the backticks ``` ` ... ` ``` do the same thing as command substitution operator ``` $( ... ) ```:
```bash
echo "Today is: $(date) . Thanks for the info."
echo "Today is: `date` . Thanks for the info."
```
**Bash** supports backticks in this context only for backward compatibility with some very old shells. There is, however, one important difference: Nesting of backticks ``` ` ... ` ``` doesn't work properly, only the nesting of command substitution operator ``` $( ... ) ``` is reliable. That being said, ``` $( ... ) ``` shall be preferably used in **Bash** instead of backticks ``` ` ... ` ```.









### 2. Input/Output (I/O) and redirections <a name="io"></a>
In the previous section we have seen how we can embed the output of one command into the input of another command with the command substitution operator ```$( ... )```. Let us now make a further progress in this direction and clarify in more detail the input and output streams of **Linux** commands. By convention, each **Linux** command has three standard input/output (I/O) channels set. More concretely, each **Linux** command has a single way of:

* accepting input : **standard input (_stdin_)** = file descriptor 0 
* producing output : **standard output (_stdout_)** = file descriptor 1
* producing error messages : **standard error (_stderr_)** = file descriptor 2

Each command that you execute has these three standard I/O channels set to some default values. By default, standard input is keyboard (but it can be also a file redirection, touchscreen, etc.). On the other hand, standard output and standard error are by default set to screen. The most important things to remember is:

* _stdout_ (file descriptor 1): This is the textual stream you see in the terminal if a command executed successfully;
* _stderr_ (file descriptor 2): This is the textual stream you see in the terminal if a command failed (a.k.a. error message).

For instance, when the command **date** executes successfully, it produces the following:
```bash
date
Sun May 17 11:53:03 CEST 2020
```
The above printout is an example _stdout_ stream of command **date**.  On the other hand, when the command **date** fails, for instance when it is called with the flag which is not supported:
```bash
date -q
```
it will print the error message:
```bash
date: invalid option -- 'q'
```
The above printout is an example _stderr_ stream of command **date**. This behaviour is true for basically all **Linux** commands.

Since these two streams, _stdout_ and _stderr_, are always set for a command, we will now see how to handle them programmatically. In practice, one can programmatically fetch the _stdout_ of some command, parse through it, and depending on its content, issue some specific action. In the similar fashion, one can fetch programmatically _stderr_ (i.e. error message) of some  command, and depending on its content, issue some specific action to fix that particular problem. For that sake, we need to use their respective file descriptors. The following operators are available in **Bash** to handle _stdout_ and _stderr_ streams:

* ```1>``` : captures and redirects to file only the successful output of command (_stdout_)
* ```2>``` : captures and redirects to file only the error message if the command failed (_stderr_)
* ```&>``` : captures and redirects to the same file both the successful output (_stdout_) and the error message (_stderr_)

For instance, if we want to redirect the _stdout_ stream of **date** command into a file, we would use:
```bash
date 1> output.log
```
Whatever the command **date** was printing on the terminal, now is re-directed to the physical file named ```output.log```. If that file doesn't exist, it will be automatically created at this point. The file's location in the file system can be specified also in this context both with an absolute and a relative path. If you now execute:

```bash
cat output.log
```

you get back the output of **date** command:

```bash
Sun May 17 11:53:03 CEST 2020
```

In this sense, by using ```1>``` redirection, the printout of some command during execution is stored permanently in the physical file on a local disk.

In an analogous way, we can also programmatically redirect the error message of a command, just need to change the file descriptor:

```bash
date -q 2> error.log
```
It's perfectly feasible to combine both examples on the same line:
```bash
someCommand 1> output.log 2> error.log
```
We can also redirect both _stdout_ and _stderr_ in the same file with ```&>``` operator:
```bash
someCommand &> outputAndError.log
```

This way we can keep the whole printout which a command has produced during execution permanently in some local files, separately for _stdout_ and _stderr_, or combined together. Then later at any point by inspecting those printouts in the files we can trace back the whole execution, which helps enormously the code development and debugging.

If we re-execute the above examples, the previous content of specified files will be overwritten with the new information. If instead you want the new information to be appended to the existing content of those files, use instead the operators: ```1>>```, ```2>>``` and ```&>>```. 

If the file descriptor number is not specified, it is defaulted to 1, i.e. ```>``` is exactly the same as ```1>```, and ```>>``` is exactly the same as ```1>>```.

Especially in the older **Bash** scripts you will see also ```2>&1``` redirection, but it has exactly the same meaning as ```&>```, which was added only in more recent versions of **Bash**. The redirector ```2>&1``` means literally: Send _stderr_ (file descriptor 2) to the same place where _stdout_ (file descriptor 1) has been sent. When ```2>&1``` is used, the order matters --- first we need to indicate where ```1>``` is redirected, and only then ```2>&1``` makes sense to use. Because of this limitation, in practice it's much easier to use ```&>``` in such a context.

 ![](blackHole.jpg)

There is also a black hole in **Linux**, and it is called ```/dev/null```. It happens frequently that you do not want to see the useless printout of some verbose command in the terminal, and you do not want to waste the disk space either to redirect it to some file. Quite frequently, some commands can print some warnings on the screen, after you have acknowledged them and concluded these warnings are harmless, you do not want to see those warnings again and again. This is precisely where the special file ```/dev/null``` becomes very handy, because whatever you redirect to it, it is lost forever.

**Example** How to redirect only the successful output of a command to a file, and ignore completely the error messages (which are sometimes just the very annoying and harmless warnings)? This request is solved with the following code snippet: 

```bash
someCommand 1>someFile 2>/dev/null
```

With the above construct, the file ```someFile``` will contain only the successful output of ```someCommand```. On the other hand, all error messages are permanently lost, because they were redirected to the ```/dev/null```.

Finally, let us also say a few words about the last file descriptor 0, _stdin_ ('standard input'). In general, _stdin_ comes from the keyboard, but we can also feed a command with the content of some file. Schematically, we would use:

```bash
someCommand < someFile
```
The operator ```<``` redirects the content of ```someFile``` into the argument of ```someCommand```. In fact, ```<``` is nothing but the shortcut synonym for ```0<``` redirection. Because a lot of commands, e.g. **cat** or **more**, expect by default an input from a file, the below three versions are all equivalent:
```bash
cat someFile
cat < someFile
cat 0< someFile
```

When you are checking the content of some file with **cat**, you are essentially redirecting its content into _stdin_ (file descriptor 0) for the **cat** command. 







### 3. Code blocks: **{ ... }** <a name="code_blocks"></a>

Clearly, all three file descriptors are extremely nice feature, but it would be even nicer if we would be able to use them to handle the output streams of multiple commands in one go, instead of redirecting the output stream of each command separately. This is possible in **Bash** by using the _code blocks_.

Code block is basicaly any sequence of commands within curly bracess ```{ ... }```. Few important remarks about the code block:
a) ```{ ... }``` inherits the environment and CAN modify it globally
b) ```{ ... }``` has its own 1> and 2> streaming facilities
c) ```{ ... }``` does NOT launch a separate process. Therefore, the rest of the script needs to wait for all commands in the code block to finish

Consider the following code snippet:
```bash
echo "before code block"
{
 echo "inside code block"
 date 
 dateee # intentionally mistype something here
} 1>output.log 2>error.log 
```
If we now check the content of files _output.log_ and _error.log_ with **cat**, we find the following in _output.log_:
```bash
inside code block
Do 23. Mai 08:56:49 CEST 2019
```
and in _error.log_
```bash
dateee: command not found
```
On the other hand, on the scren the only printout is:
```bash
before code block
```
Another piece of code in the same script can be embeded in another code block, and redirected to another files. This way we can easily profile the code with redirectors, and decide what goes in the screen and what is arxived in files. 
Tipically, code blocks ```{ ... }``` are used when it's not beneficial to break down the whole script into functions.

To check the influence of code block on the environment, please execute the following code snippet:
```bash
Var=44
echo "Before : $Var"
{
 echo "Inside : $Var"
 Var=55
}
echo "After : $Var"
```
Upon execution, this code snippet produces:
```bash
Before : 44
Inside : 44
After : 55
```
So, the code block inherits all settings from the global environment, and all modifications made inside the code block are propagated outside to the global environment. Some of these remarks can be relaxed by enclosing the code block within different type of braces, namely ```( ... ) ```, to define the _subshells_---this will be covered later. 

Very importantly, **for** and **while** loop have their own _stdout_ and _stderr_ streams, which you can redirect to the output files with ```1>``` and ```2>``` operators. In this way, you can disentangle what is happening in a particular loop, from what is happening in the rest of the script. Schematically, we would use for **for** loop:
```bash
for Var in some-list; do
 ... some commands ...
done 1>output.log 2>error.log
```
We can simultaneously feed **while** loop from external fie, and redirect its _stdout_ and _stderr_ also in external files, schematically:
```bash
while read Line; do
 ... some commands ...
done <someFile.log 1>output.log 2>error.log
```
This way for instance, we can parse and modify programmatically the current file 'someFile.log' line-by-line, and save the modified new file in 'output.log'.





**Example TBI:** Code block within the command chain.

```bash
someCommand && { command1; command2; ... } || { command1; command2; ... } 
```





As a side remark, curly braces are also used in a completely different context, to define easily the sequences, via the so-called _brace expansion_. Please try these examples:

```bash
touch File.{log,png,pdf}
```
This automatically creates three files with the same name, but different exensions: ```File.log```,  ```File.png``` and ```File.pdf```.
The syntax to generate sequences is demonstrated in the following examples:
```bash
echo {1..10}
echo {1..10..2}
echo {10..1}
echo {a..f}
echo {-4..4}
```
The printouts are:
```bash
1 2 3 4 5 6 7 8 9 10
1 3 5 7 9
10 9 8 7 6 5 4 3 2 1
a b c d e f
-4 -3 -2 -1 0 1 2 3 4
```
Brace expansion is very frequently used in enumerating sequentially either files or directories. For instance:
```bash
mkdir Dir_{0..9}
```
will in one go and effortlessly create 10 directories named ```Dir_0```, ```Dir_1```, ... ```Dir_9```.  We can both prepend and append strings:
```bash
touch File_{0..9}.log
```
This will create 10 new files sequentially named ```File_0.log```, ```File_1.log```, ... ```File_9.log```. 

Having directories or files named sequentially, we can easily manipulate only a subset. For instance, if we have some sequentially named files.

**Example 2**: Imagine that in some directory you have the following situation:
```bash
File_1.log File_2.log File_3.log ... File_9999.log
File_1.inf File_2.inf File_3.inf ... File_9999.inf
File_1.dat File_2.dat File_3.dat ... File_9999.dat
```
How to delete each 4th file, from 111 to 222, whose extension is either .log or .inf? If you use brace expansion, the solution is trivial:
```bash
rm File_{111..222..4}.{log,inf}
```
Without brace expansion, the solution would take some work as you would need to set up the script with loops and string comparisons, etc.







### 4. Conditional statements <a name="conditional_statements"></a>
It is possible also in **Bash** to branch the execution of your code, and it works very similar like in the other programming languages. For simpler cases we would use **if-elif-else-fi** conditional statement, while the syntax of **case-in-esac** conditional is more suitable for more complex cases.

We use **if-elif-else-fi** to branch the code execution after checking the outcome of test construct ```[[ ... ]]```, i.e. schematically:
```bash
if [[ some-expression ]]; then
  some code when this test is true
elif [[ some-other-expression ]]; then 
  some code when this test is true
 ...
else 
  some code when all tests above are false
fi
```
You can have as many **elif**'s as you wish, but the last statement shall be **else**.

Another typical use case is to branch the code execution depending on whether command or function execution succeeded or failed, schematically:
```bash
if some-command; then
  some code when this command worked
elif some-other-command; then 
  some code when this command worked
 ...
else 
  some code when all commands above failed
fi
```
If interested only in checking the exit status of command, and want to suppress any output stream when executing that command, that can be achieved with:
```bash
if some-command &>/dev/null; then
```
In the same way you can use all other file descriptors, like ```1>``` and ```2>```, as a part of **if** or **elif** statement.


On the other hand, the syntax of **case-in-esac** conditional statement is more elaborate, but more powerful. Schematically, for most frequent use cases it looks like:
```bash
case some-value in 
 first-possibility) some code when this possibility is met ;;
 second-possibility) some code when this possibility is met ;;
 *) some code when non of specified possibilities above is met ;;
esac 
```
The thing to remember is that in **case-in-esac** conditional statement one specific code block is embedded within ```)``` and ```;;``` (yes, double semicolon, no empty character is allowed among them!).

Example code snippet to illustrate the syntax: 
```bash
Flag=$1
case $Flag in
 -a) 
     echo "You have specified option -a for your script/function."
     echo "For the option -a we do the following..." 
  ;;
 -b) 
     echo "You have specified option -b for your script/function."
     echo "For the option -b we do the following"   
  ;; 
  *) echo "Specified flag not supported"
     return 1
  ;;
esac
```
In this way, with the **case-in-esac** conditiona; statement (additionally embedded in the **for** loop for instance) you can in the simplest way change the default behavior of your script or function, depending on which flags (options) user has supplied. For more elaborate cases to parse command line arguments in such context, see **Bash** built-in command **getopts**.

Multiple statements can be grouped with ```|``` (OR), schematically:
```bash
case some-value in 
 first-possibility|second-possibility) some common code when either first or second possibility is met ;;
 *) some code when neither first or second possibility is met ;;
esac 
```
The **case-in-esac** conditional statement recognizes the so-called POSIX brackets, few most important examples are: 
o ```[[:alpha:]]``` Alphabetic characters	[a-zA-Z]
o ```[[:digit:]]``` Digits [0-9]
o ```[[:alnum:]]``` Alphanumeric characters	[a-zA-Z0-9] 

Example use case:
```bash
Var=4
case $1 in
 [[:alpha:]]) 
             echo "Var is a single alphabetic character" 
           ;;
 [[:digit:]]) 
             echo "Var is a single digit" 
           ;;
 *) 
   echo "Var is something else" 
 ;;
esac
```

As the final remark, when developing the code using conditional statements, sometimes we are not sure immediately what to implement in the particular branch. We cannot leave that branch empty or only put a commentas both cases produce error:
```bash
if [[ 2 -gt 1 ]]; then
 # I will implement this part later 
fi 
```
The error message is:
```linux
bash: while.sh: line 4: syntax error near unexpected token `fi'
bash: while.sh: line 4: `fi '
```
For this sake, we need to use the  so-called 'do-nothing' command ```:``` as a placeholder.

So the correct solution to the above problem would be:
```bash
if [[ 2 -gt 1 ]]; then
 : # I will implement this part later 
fi 
```
'Do nothing' command ```:``` does literally nothing, except that it always returns the exit status 0, i.e. it always succeeds in what it needs to do, which is not surprising given that fact that it does nothing. Quite remarkably, even such a simple command has some interesting and frequent use cases.

**Example 1:** How to empty the already existing file, preserving all its permissions?
```bash
: > someFile.log
```
Literally, we have redirected nothing into the file, so its content is now nothing, while preserving the same file permissions. The above simple line is equivalent to: 
```bash
Var=$(some-code-to-extract-and-save-the-permissions-of someFile.log)
rm someFile.log
touch someFile.log # empty file is created, but with some default permissions 
chmod get-permissions-from-$Var someFile.log
```

**Example 2:** Infinite loop in **Bash**.

The simplest implementation is:
```
while :; do
 some-commands
done
```

We can also use 'do-nothing' ```:``` command to write a multi-line comment in **Bash** in combination with the so-called _here-documents_---thsi will be covered later.



