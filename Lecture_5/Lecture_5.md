![](bash_logo.png)


# Lecture 5: Command substitution. Input/Output (I/O). Conditional statements

**Last update**: 20200513


### Table of Contents
1. [Command substitution: **$( ... )**](#command_substitution)
2. [Input/Output (I/O) and redirections](#io)
3. [Conditional statements](#conditional_statements)






### 1. Command substitution: $( ... ) <a name="command_substitution"></a>
We have already seen that a value can be stored in a variable by explicit assignment (using the operator ```=```),  or of the user supplies variables as command line arguments (positional parameters) to a script or a function. In practice, however, one frequently wants to store the output of some command directly into the variable, or even the content of an external file. This can be achieved with the so-called _command substitution operator_ ```$( ... )```.  For instance, we have already seen that the file size in bytes can be printed with the following:

```bash
stat -c %s someFile.log
```
But how can we fetch the above printout programmatically, and do some manipulation with it later in our code? This is precisely the case where we need to use the command substitution operator:
```bash
FileSize=$(stat -c %s someFile.log)
```
Now the size of file ```someFile.log``` is stored directly in the variable **FileSize** and from this point onwards we can reference content of that variable in the same way as the content of any other variable:
```bash
echo ${FileSize}
```

The operator ```$( ... )``` can do much more than that. For instance, it can literally in-line the output of any command at the place where this operator was used. 

**Example 1**: How to produce the following single-line output, with the current time stamp embedded:
```bash
Today is Mo 20. Mai 15:33:07 CEST 2019 . What a nice day...
```
This can be achieved with:
```bash
echo "Today is $(date) . What a nice day..."
```
The command substitution operator literally in-lined the output of **date** command at the place where it was used. This way, we can very elegantly achieve the desired more complex functionality by combining in the very same command input multiple commands, which otherwise we would need to execute one-by-one. 

Command substitution operator ``` $( ... ) ``` is a very neat construct, and it is used more frequently than you would expect. One classical use case is to avoid hardwiring any user specific information in your code, since it can change from one computer to another.

**Example 2**: How to store programmatically your user name, in case the environment variable **USER** is not set?

```bash
UserName=$(whoami) 
```
You can nest fearlessly command substitution operators, like in the following example. 

**Example 3**: How can you get programmatically only the name of the parent directory of the directory in which your script sits (knowing that env. variable **PWD** holds only the absolute path of script's directory)? 

In order to solve this problem, we need first to introduce two widely used **Linux** commands in this context: **basename** and **dirname**. The command **basename** is typically used in the following way: It takes as an argument the absolute path to some directory or file, and drops the absolute path part, i.e. it leaves only the base directory or file name. This is illustrated with the following code snippets:
```linux
SomeDirectoryPath=/home/abilandz/Lecture/SS2019/Lecture_5
basename $SomeDirectoryPath # prints Lecture_5
```
and for files:
```linux
SomeFilePath=/home/abilandz/Lecture/SS2019/Lecture_5/someFile.log
basename $SomeFilePath # prints someFile.log
```
On the other hand, the command **dirname** does the opposite: It prints only the absolute path part to the specified directory or file. For instance: 
```linux
SomeDirectoryPath=/home/abilandz/Lecture/SS2019/Lecture_5
dirname $SomeDirectoryPath # prints /home/abilandz/Lecture/SS2019
```
and for files:
```linux
SomeFilePath=/home/abilandz/Lecture/SS2019/Lecture_5/someFile.log
dirname $SomeFilePath # prints also /home/abilandz/Lecture/SS2019/Lecture_5
```

Therefore, the solution to our initial problem is fairly elegant and concise, if we use these two commands in combination with command substitution operator:
```bash
ParentDirectoryName=$(basename $(dirname $PWD))
```
or a bit less efficiently (just to demonstate that nesting of command substitution operators is not a problem):
```bash
ParentDirectoryName=$(basename $(dirname $(pwd)))
```

We can use multiple commands within same command substitution operator, e.g.:
```bash
echo $(date; pwd; ls)
```
but such generalization is not used that frequently in practice. 

It is perfectly fine to inline the output of your function with this operator, e.g.:
```bash
echo "Output of my function is: $(someFunction) . Very nice!" 
```

Finally, the very neat use case of command substitution operator is to  put the content of some external file in the variable. The relevant syntax is:
```bash
FileContent=$(< someFile.log) 
```
In the above example, ```<``` is just a shortcut for command **cat**, which can be used as well in this context directly. This great funcionality circumvents the neccessity of dealing with too many temporary physical files during code execution, when we are interested to keep the file content only at particular time. With the above definition, the following two commands yield exactly the same answer initially:
```bash
cat someFile.log # reads the content of physical file
echo "$FileContent" # references the content of variable
```
However, if the content of pysical file ```someFile.log``` changes, that change does not affect the value of variable **FileContent**, and we did not need to create physically any temporary file to achieve that.

Command substitution operator is frequently used in combination with **for** loop, when we want to iterate over all elements in the output of some command. Also in this context distinct elements of the list are separated with one or more empty characters. This is best illustrated with the following example:

**Example 4**: How can we loop over all files in the current directory and do something with each of them?

One example solution is provided with the following code snippet:
```bash
for File in $(ls $PWD); do
 echo $File
 echo -e "It's size is: $(stat -c %s $File) \n" 
done
```
Note that if you would have used the lengthy output of **ls**, e.g. by specifying flags **-al**, then variable 'File' would loop over all entries in the command output separated with one or more empty characters, also over the permission field, user name etc.  This is illustrated also in the following example:
```bash
date
echo "Looping over all entries in the 'date' output:"
for Var in $(date); do
 echo $Var
done
```
The output is:
```linux
Fr 24. Mai 08:25:16 CEST 2019
Looping over all entries in the 'date' output:
Fr
24.
Mai
08:25:16
CEST
2019
```
This is another example of understanding the importance of empty character being the default field separator in **Linux/Bash**.

At the end, we would like to remark that backticks ``` ` ... ` ``` do the same thing as command substitution operator ``` $( ... ) ```, please compare:
```bash
echo "Today is: $(date) . Thanks for info."
echo "Today is: `date` . Thanks for info."
```
There is, however, one important difference: Nesting of backticks ``` ` ... ` ``` doesn't work properly in each case, only the nesting of command substitution operator ``` $( ... ) ``` is reliable. That being said, ``` $( ... ) ``` shall be used instead of ``` ` ... ` ``` almost exclusively.







### 2. Input/Output (I/O) and redirections <a name="io"></a>
In the previous section we have seen how we can embed the output of one command into the input of another command with the command substitution operator---let us now clarify in more detail the input and output streams of **Linux** commands.  By convention, each **Linux** program has three standard input/output (I/O) channels set:

* a single way of accepting input called standard input (_stdin_) --- file descriptor 0 
* a single way of producing output called standard output (_stdout_) --- file descriptor 1
* a single way of producing error messages called standard error (_stderr_) --- file descriptor 2

Each program that you invoke has these three standard I/O channels set to some default values. By default, standard input is keyboard (but also can be file redirection, touchscreen, etc.). On the other hand, standard output and standard error are by default set to screen. The most important thing to remember is:

* _stdout_ (file descriptor 1): This is the textual stream you see on the terminal if command executed successfully;
* _stderr_ (file descriptor 2): This is the textual stream you see on the terminal if command failed (a.k.a. error message).

For instance, when the command **date** executes successfully, it produces the following:
```bash
date
Fr 23. Nov 14:21:42 CET 2018
```
The above printout is an example _stdout_ stream of command **date**.  On the other hand, when the command **date** fails, for instance when it is called with the flag which is not supported:
```bash
date -q
```
it will print the error message:
```bash
date: invalid option -- 'q'
```
The above printout is an example _stderr_ stream of command **date**. This behaviour is true for basically all **Linux** commands. Since these two streams, _stdout_ and _stderr_, are always available, now we will learn how to handle them programatically. In practice, one can programatically fetch the _stdout_ of some command, parse it and depending on its content, issue some specific action. In the similar fashion, one can fetch programmatically _stderr_ ('error message') of some  command, and depending on its content issue some specific action to fix the problem.

How can we programmatically handle those _stdout_ and _stderr_ streams? For that sake, we need to use their respective file descriptors. The following operators are available in **Bash** to handle _stdout_ and _stderr_ streams:

* ```1>``` : captures and redirects to file only the successful output of command (_stdout_);
* ```2>``` : captures and redirects to file only the error message of command (_stderr_);
* ```&>``` : captures and redirects to the same file both the successful output (_stdout_) and the error message (_stderr_).

For instance, if we want to redirect the _stdout_ stream of command into the file, we would use:
```bash
date 1> output.log
```
Whatever the command **date** was printing on the terminal, now is re-directed to the physical file 'output.log' (please see its content with **cat**). In an analogous way, we can also programmatically redirect the error message, just need to change the file descriptor:
```bash
date 2> error.log
```
It's perfectly feasible to combine both examples on the same line:
```bash
date 1> output.log 2> error.log
```
We can also redirect both _stdout_ and _stderr_ in the same file with ```&>``` operator:
```bash
date &> outputAndError.log
```

This way we can keep the whole information which our command has produced during execution permanently in the files, separately for _stdout_ and _stderr_, or combined together. If we re-execute the above examples, the existing content of physical files will be overwritten with new information. If instead you want new information to be appended to the existing content of specified file, use instead the operators: ```1>>```, ```2>>``` and ```&>>```. If the specified file doesn't exist, it will be automatically created in the directory where the command was executed.

If file descriptor number is not specified, it is defaulted to 1, i.e. ```>``` is exactly the same as ```1>```, and ```>>``` is exactly the same as ```1>>```.

There is also ```2>&1``` redirection which is frequenty used, but it's exactly the same as  ```&>``` (the second one was added later in the **Bash**, so the first one is more portable in a sense that only that one can be used in some older **Bash** versions). The redirector ```2>&1``` says literally: Send _stderr_ (file descriptor 2) to the same place as _stdout_ (file descriptor 1). When ```2>&1``` is used, order matters---first we need to indicate where 1> is going, and only then 2>&1 makes sense to use.


 ![](blackHole.jpg)

Yes, there is also black hole in **Linux**, just it is called ```/dev/null```.  What for instance if you couldn't care less about the printout of your command on the terminal, and you do not want to waste the disk space either to redirect it to some files? No worries, anything you redirect to ```/dev/null``` is lost forever.

**Example 1:** What do you need to do if you want to redirect only the successful output of your command to the file, and you do not want to see the error messages (sometimes the very annoying and harmless warnings are classified this way!) neither on the terminal, nor in the file? Then schematically you would do:

```bash
someCommand 1>output.log 2>/dev/null
```

Let us also say a few words of the last file descriptor 0, _stdin_ ('standard input'). In generlal, _stdin_ comes from the keyboard, but we can also feed the command with the content of some file. Schematically, we would use:
```bash
someCommand < someFile
```
The operator ```<``` redirects the content of file as the argument to command. This operator is the shortcut synonym for ```0<```. Since some commands, e.g. **cat** expect by default input from the file, all these versions are giving exactly the same answer:
```bash
cat someFile
cat < someFile
cat 0< someFile
```

Clearly, file descriptors are extremely nice feature, but it would be even nicer if we would be able to use them to handle  e.g. the output streams of multiple commands, or code blocks, in one go, instead of redirecting the output streams of each command separately---this is possible in **Bash** by using the code blocks!

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





 




### 3. Conditional statements <a name="conditional_statements"></a>
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



