![](bash_logo.png)

# Lecture 9: Real-life examples

**Last update**: 20200622

### Table of Contents
1. [Command history search](#command_history_search)
2. [Searching for files and directories: **find**](#find)
3. [Online monitoring: **tail -f**](#tail)
4. [Timing: **timeout** and **time**](#timing)
5. [Counting: **wc**](#counting)
6. [Building programmatically command input: **eval**](#eval)



### 1. Command history search <a name="command_history_search"></a>
When working directly in the terminal we want to speed up the command input as much as possible. Besides, frequently we want to be able to re-use the certain command input again, without re-typing it from scratch. A lot of typing is saved by using ```TAB```, which autocompletes the command input to existing commands names (here commands are meant in the broader sense and include also **Bash** functions, aliases, etc.). In the case command is expecting as argument a file or directory, ```TAB``` will also autocomplete file or directory name, after we have typed in the first few characters and hit ```TAB```. Last but not least, ```TAB``` also autocompletes the **Bash** variable names when we are referencing their content. This is illustrated with the following three simple examples:
```bash
dirn + TAB 
# autocompletes to command 'dirname'

cat fileW + TAB  
# autocompletes to file named 'fileWithLengthyName.log'

LongNameVariable=44
echo $Lo + TAB 
# autocompletes to 'echo $LongNameVariable'
```
In general, when there are multiple matches for the initial pattern, after pressing once the ```TAB``` nothing happens, however pressing two times ```TAB TAB``` lists all matches:
```bash
ls Lecture_9 + TAB + TAB 
# prints Lecture_9.md Lecture_9.html Lecture_9.pdf  
```
The precedence of text completion via ```TAB``` can be summarized as follows: commands and functions first, then files and directories. If we want to alter these default precedence rules in some special cases of interest, we can use the following three special characters:  

* if the text is preceded with ```$``` : variable completion with ```TAB``` takes precedence  
* if the text is preceded with ```~``` : username completion with ```TAB``` takes precedence  

The autocompletion via ```TAB``` is a very neat feature and speeds up a lot the typing, but it cannot help us to re-use what we have already typed. 

To achieve that, we need to use **Bash** built-in command **history**.  After we type in the terminal
```bash
history
```
all the command input which we have typed in the terminal recently (not necessarily only in the current terminal!) will be printed and enumerated by **Bash**. For instance, the output could be:

```bash
  525  ls
  526  nano readMe.txt
  527  ls
  528  tar -czf Lecture_8.tar.gz Lecture_8/
  529  ls
  530  cd ..
  531  ls
  532  typora Homework_7.md &
  533  cd ../Lecture_9
```
From where **Bash** has retrieved this detailed information of what we typed of late in the terminal? All previously typed commands are stored by default in the file to which the environment variable **HISTFILE** is pointing to:
```bash
echo $HISTFILE
```
The printout could look like:
```bash
/home/abilandz/.bash_history
```
That means that, by default, the history of all our command input is saved in the file ```.bash_history``` placed in the home directory. By default, at maximum 1000 lines of command input are kept in this file, but that can be changed by modifying the **Bash** environment variable **HISTSIZE**. If we now have a look at the content of the **Bash** command history file:
```bash
cat /home/abilandz/.bash_history
```
we see that we get a similar printout like the one from the command **history** showed above. The printout is similar, but not the same, and we will now clarify this difference, which sometimes leads to big confusion. 

Each time we start a new terminal, the file ```~/.bash_history``` is read. From that point onward, each terminal maintains its own history (i.e. its own list of all commands we have typed in the terminal). When we exit the terminal, **Bash** updates the ```~/.bash_history``` file with the history which corresponds to that terminal. Therefore, and very importantly, the current content of ```~/.bash_history``` will correspond to the last terminal we have closed. 

Some frequently used flags for the command **history** and their meanings are summarized here:  

* ```-c``` : clears the history list (but it doesn't clean the content of ```~/.bash_history``` instantly, remember that this file gets updated automatically only after we exit the terminal!)   
* ```-d someNumber``` : clears the **history** entry only at the line 'someNumber'   
* ```-a``` : forces appending history lines from the current terminal to the history file ```~/.bash_history```. With this option, we save permanently all commands we have typed in the history file, even without exiting the terminal

After understanding the **history** mechanism, we now demonstrate how we can directly extract only the entry we need with a few convenient shortcuts:  

* Use up and down arrow (or equivalently ```Ctrl+N``` and ```Ctrl+P```) in the terminal to browse through (in the specified order!): 

  * terminal's own history
  * the content of  ```~/.bash_history```   

* ```Ctrl+R``` : inverse history search. After we press ```Ctrl+R```, the following prompt appears:

  ```bash
  (reverse-i-search)`':
  ```

  Now we can type the pattern which will be used to search for some previously used command input that contained that pattern. We can keep pressing ```Ctrl+R```, until the command input we are looking for appears. By pressing the right arrow, that command input is copied in the terminal, and we can now reuse it again. 

**Example:** The inverse history search is an extremely handy feature, and we now illustrate it with the concrete example. Imagine a scenario in which we have typed in the terminal for** loop, followed by a lot of other commands:

```bash
for i in {1..10}; do echo $i; done
... one zillion other commands ...
```

Do we need to retype the whole **for** loop from scratch, in case we need that command input again? It suffices only to do the following:

```bash
Ctrl+R
(reverse-i-search)`': # type the pattern'fo'
(reverse-i-search)`fo': for i in {1..10}; do echo $i; done
```

Press the right arrow, and the offered result from the inverse history search is copied in the terminal, and can be reused. If the offered result from the inverse history search is not what we need, we can keep pressing ```Ctrl+R``` to browse through all results which match the specified pattern. 

We indicate now how we can directly re-execute any command input from the history list. For instance, if the command

```bash
history
```
has produced the following output
```bash
  188  ls
  189  grep Ctrl Lecture_?/Lect*
  190  for i in {1..10}; do echo $i; done
  191  ls
  192  pwd
```
we can execute any command from above just by typing ```!commandNumberInTheList```. Given the above output, the following input in the terminal: 
```bash
!190
```
gives immediately
```bash
for i in {1..10}; do echo $i; done
1
2
3
4
5
6
7
8
9
10
```
For more elaborate cases of retrieving and even editing the command input from **history**, please see the documentation of the command **fc** ('fix command').

Finally, we remark that programmatically we can retrieve the last argument of the previously executed command. This functionality is achieved via the special Bash variable ```$_```. If the previously executed command has only one argument, then the content of ```$_``` is that argument. For instance:

```bash
mkdir Dir1 Dir2 Dir3
echo $_ 
# prints Dir3
```

A frequent use case is the following example:

```bash
ls file_{1..99}.log
rm $_
```

If the first line has expanded in the list of files we want to delete, we can reuse the same brace expansion n the second line as the argument for **rm** command.

**Example:** How to make a few directories, and automatically move into the last one created?

```bash
mkdir Dir1 Dir2 Dir3 && cd $_
```










### 2. Searching for files and directories: **find** <a name="find"></a>

We have already seen how we can list the content of the specified directory with **ls** command.  However, in the case we need to search for specific files or directories which are somewhere in the filesystem hierarchy, **ls** command is useless. Instead, the **find** command is **Linux** utility which was designed precisely for that sake. It can for instance perform search by name, creation, accession and modification date, owner and permissions. In addition, **find** can immediately perform some action on the result of its search (e.g. it can immediately delete all files it has found, rename all directories, etc.).

Generic usage of **find** command can be described as folows:
```bash
find  Where  What  OptionallyDoSomethingOnWhatYouHaveFound
```
Whent interpreting its arguments, **find** assumes that you started by specifying directories where the search will be performed, therefore the label ```Where``` in the above generic syntax stands for one or more directories. After that, **find** expects one or more options, which will typically nail down what it needs to search for (label ```What``` used above). Finally, there exists a special option **-exec** after which we can optionally set the commands which **find** will execute immediately on the results it has found.

The usage of **find** is best illustrated on concrete examples. Let us start by assuming that in some directory named 'Examples' we have the following situation:
```bash
$ ls Examples/
Directory_0  Directory_1  Directory_2  Directory_3  file_0.dat  file_0.pdf  file_0.png  file_1.dat  file_1.pdf  file_1.png
```

**Example 1:** Find all files in the specified directory.

```bash
find Examples/ -type f
```
The result is:
```bash
Examples/file_0.png
Examples/file_1.dat
Examples/file_1.pdf
Examples/file_1.png
Examples/file_0.dat
Examples/file_0.pdf

```
By default, the result of **find** is not sorted, but you can trivially and in general 'fix' that by piping to command **sort**, e.g. by using:
```bash
find Examples/ -type f | sort
```
The output is now sorted:
```bash
Examples/file_0.dat
Examples/file_0.pdf
Examples/file_0.png
Examples/file_1.dat
Examples/file_1.pdf
Examples/file_1.png
```

**Example 2:** Find all subdirectories in the specified directory.

```bash
find Examples/ -type d
```
The result is:
```bash
Examples/Directory_3
Examples/Directory_2
Examples/Directory_0
Examples/Directory_1
```

**Example 3:** Find all files with an extension .pdf' in the specified directory

```bash
find Examples/ -type f -name "*.pdf"
```
The result is:
```bash
Examples/file_1.pdf
Examples/file_0.pdf
```

**Example 4:** Find all files with an extension '.pdf' and pattern 'file_0' in their name, in the specified directory.
```bash
find Examples/ -type f -name "*.pdf" -a -name "*file_0*"  
```
The result is:
```bash
Examples/file_0.pdf
```
Since the flag ```-name``` is very frequently used, it deserves some additonal clarification. The usage of double quotes in the pattern (e.g. in ```"*.pdf"```) was essential, since now the special characters, like ```*```, will be passed as special characters to the **find** command, before **Bash** expands them. The following wrong syntax
```bash
find Examples/ -type f -name *.pdf # WRONG!!
```
would expand first ```*.pdf``` to all files in the current directory ending in ```.pdf```, and only then those full expanded file names would be supplied to the command **find**. Clearly, this can work only by accident, i.e. if in the current working directory where we have executed command **find** there was no a single file which ends in ```.pdf```, and therefore ```*.pdf``` remained unexpanded. Alternatively, the special symbols can be passed literally to **find** with the escaping mechanism using ```\```. Summarizing everything:
```bash
find Examples/ -type f -name "*.pdf" # CORRECT
find Examples/ -type f -name '*.pdf' # CORRECT
find Examples/ -type f -name \*.pdf # CORRECT
find Examples/ -type f -name *.pdf # WRONG!!
```
Some other frequently used options of **find** command are illustrated with characteristic examples below.

**Example 5:** Find all files with an extension '.pdf' larger than 10 KB in the specified directory.

```bash
find pathToDirectory(-ies) -type f -name "*.pdf" -size +10k
```
Here prefix ```+``` is not trivial, if we would omit it, the flag ```-size 10k``` would filter out instead the files whose size is exactly 10 KB. Unfortunately, syntax for KB in **find** is a small 'k', and not capital 'K' (like e.g. in **ls -lh**), which frequently leads to confusion.  Analogously, files which are smaller then 10 KB in size, we would filter out by using prefix ```-```, e.g. ```-size -10k```.

**Example 6:** Find all files with an extension '.tex' modified within last 10 days, let's say.
```bash
find pathToDirectory(-ies) -type f -name "*.tex" -mtime -10
```
Similiarly as with the option ```-size```, the option ```-mtime +10``` means more than 10 days, ```-mtime 10``` means exactly 10 days ago, ```-mtime -10``` means less than 10 days ago. Closely related flags are ```-atime```  and ```-ctime```. The flag ```-atime``` traces when the files were last accessed (i.e. read without being modified, for instance using **cat** command), while the flag ```-ctime``` traces when the file's physical properties (e.g. its content,  permissions, name, location) were last time changed. If you edit and save the file, all three flags ```-mtime```, ```-ctime``` and ```-atime``` are updated. If you only open and view the content of the file with **cat** or some other viewer, only the flag ```-atime``` is updated. 

**Example 7:** Find all obsolete files in some directory(-ies) which were not accessed for more than 1 year.
```bash
find pathToDirectory(-ies) -type f -atime +365
```
The three popular flags ```-mtime```, ```-ctime``` and ```-atime``` have the lowest resolution of 1 day. If for instance you want to get the search with even finer time resolution in minutes, you need to use the corresponding flags ```-mmin```, ```-cmin``` and ```-amin```.

By default, the command **find** searches through all subdirectories of specified directory. Clearly, especially if we start the search in some top level directory in file hierarchy, the search can take forever. If we are sure that the targeted files are not deeper in the directory structure than certain level, we can use flags ```-maxdepth``` and ```-mindepth``` to greatly optimze the search.

**Example 8:** Find all files with an extension '.pdf' in the specified directory, not going deeper than 2 levels in the subdirectory structure.
```bash
find pathToDirectory(-ies) -maxdepth 2 -type f -name "*.pdf"
```

**Example 9:** Find all files with an extension '.pdf' in the specified directory, by looking only in the subdirectories (i.e. not in the current directory, and not in subsubdirectories, subsubsubdirectories, etc.)
```bash
find pathToDirectory(-ies) -mindepth 2 -maxdepth 2 -type f -name "*.pdf"
```

Finally, and very importantly, we describe the flag ```-exec``` which is used to specify the command input which **find** needs to execute on the spot on the search outcome. The syntax for flag ```-exec``` is a bit peculiar, but the two most important thingies to remember are:

* ```\;``` : the command input to ```-exec``` flag is determined this way
* ```{}``` : when used for ```-exec``` flag, this is a placeholder for found file or directory

**Example 10:** Find all empty files in the specified directory, and for each of them prints its size.

```bash
find pathToDirectory(-ies) -type f -exec stat -c %s {} \;
```
From this example its clear when and how we use the placeholder ```{}``` for the found file or directory in combination with ```-exec``` flag.

**Example 11:** Find all empty files in the specified directory, and delete them immediately:

```bash
find pathToDirectory(-ies) -type f -size 0 -exec rm {} \;
```

It is also posible to execute multiple commands on the files or directories which **find** has found, we just need to use separate ```-exec``` flag for each command input:

**Example 12:** Find all files in the specified directory, and for each of them: a) print the full metadata with **ls -al**; and b) print its size with **stat -c %s**. 

```bash
find pathToDirectory(-ies) -type f -exec ls -al {} \; -exec stat -c %s {} \;
```
Equivalently, less error prone and more elegantly, we can use **while+read** construct in combination with the process substitution operator ```<( ... )``` to achieve the same thing:
```bash
while read File; do
 ls -al $File
 stat -c %s $File
done < <(find pathToDirectory -type f)
```
The second solution is more readable and less error prone.








### 3. Online monitoring: **tail -f**<a name="tail"></a>
In general, we can view the whole content of the file with **cat** command, or if we want paging to appear one screen at a time we can use commands like **more** or **less**. On the other hand, we can select and view only part of the file in general with **sed**. For instance, if the starting file named 'example.txt' has the following content:
```bash
line 1
line 2
line 3
line 4
line 5
line 6
line 7
```
we can select with **sed** for viewing only the lines in the specified range (both ends included), like in the following example:
```bash
sed -n 2,4p example.txt
```
Flag ```-n``` ensures that the starting file is not superimposed with the desired selected printout, while ```p``` stands for 'print'. The results is:
```bash
line 2
line 3
line 4
```
If we are interested to print only the first 'n' lines of the file, we can use **head -n** command. For instance:
```bash
head -3 example.txt
```
will print only the first 3 lines:
```bash
line 1
line 2
line 3
```
On the other hand, if we are interested to print only last 'n' lines of the file, we can use **tail -n** command. For instance, to get programmatically only the last line in the file, you can use:
```bash
tail -1 example.txt
```
which gives for the above example:
```bash
line 7
```
But in this context, the really great use case is provided with the flag **-f** of **tail** command. Namely, with **tail -f** you can monitor online the output of files at it grows in size. That means that if you have redirected the 'stdout' stream of some command to some file, and if you execute **tail -f** on that file, you will monitor what that command is doing just like you are looking at its printout in the terminal. The beautiful thing here is that we can execute **tail -f** on that file from any terminal and monitor online what that command is doing, not necessarily from the same terminal where the command is started. This is best illustrated with the following example:
```bash
( echo ${BASHPID}; while :; do echo "1: $(date)"; sleep 10s; done; ) 1>first.log &
( echo ${BASHPID}; while :; do echo "2: $(date)"; sleep 20s; done; ) 1>second.log &
( echo ${BASHPID}; while :; do echo "3: $(date)"; sleep 30s; done; ) 1>third.log &
```
With this example, we have started off three subshells in the background, where each of them each 10s, 20s and 30s, respectively, prints the time stamp, which is redirected via ```1>``` in its own log file. Since all 3 shells are running in the background, we have the control over the terminal, and we can for instance checkout the status of jobs submitted:
```bash
jobs -l
[1]  20860 Running                 ( echo ${BASHPID}; while :; do echo "1: $(date)"; sleep 10s; done ) > first.log &
[2]- 20867 Running                 ( echo ${BASHPID}; while :; do echo "2: $(date)"; sleep 20s; done ) > second.log &
[3]+ 20873 Running                 ( echo ${BASHPID}; while :; do echo "3: $(date)"; sleep 30s; done ) > third.log &
```
The great thing now is that we can inspect online what each of these jobs are doing, for instance:
```bash
tail -f first.log
```
This gives:
```bash
1: Mi 10. Jul 12:23:51 CEST 2019
1: Mi 10. Jul 12:24:01 CEST 2019
1: Mi 10. Jul 12:24:11 CEST 2019
1: Mi 10. Jul 12:24:21 CEST 2019
1: Mi 10. Jul 12:24:31 CEST 2019
1: Mi 10. Jul 12:24:41 CEST 2019
1: Mi 10. Jul 12:24:51 CEST 2019
1: Mi 10. Jul 12:25:01 CEST 2019
```
and as the subshell execution proceeds, the output of **tail -f** gets updated on the screen automatically, just like the subshell is directly running in the terminal, and not in the background! If we now hit ```Ctrl-C```, we terminate only **tail -f** printout, without any interference with the running subshell. After terminating with ```Ctrl-C```, we can now execute:
```bash
tail -f second.log
```
This gives:
```bash
2: Mi 10. Jul 12:24:24 CEST 2019
2: Mi 10. Jul 12:24:44 CEST 2019
2: Mi 10. Jul 12:25:04 CEST 2019
2: Mi 10. Jul 12:25:24 CEST 2019
2: Mi 10. Jul 12:25:44 CEST 2019
2: Mi 10. Jul 12:26:04 CEST 2019
2: Mi 10. Jul 12:26:24 CEST 2019
2: Mi 10. Jul 12:26:44 CEST 2019
2: Mi 10. Jul 12:27:04 CEST 2019
2: Mi 10. Jul 12:27:24 CEST 2019
```
We now monitor online what the 2nd subshell running in the background is doing. Please note that this functionality works from any terminal, since viewing the content of physical files with  **tail -f** is not limited to any particular terminal. 










### 4. Timing: **timeout** and **time** <a name="timing"></a>
Frequently in practice we are faced with the situation when the command execution gets stalled, without clear indication when its execution might resume. For instance, if we are copying files over the network, and if the network connection experiences a problem, the copying itself will hang until the network connection recovers. But for instance if are copying over network 100 files containing our data, and if we managed to copy > 90% of them, clearly we can reach the decent statistics and realible results in our analysis, even if we didn't analyze the whole dataset. 

In general, we can prevent command to hang forever with the **timeout** command. This command in essence ensures that some command is run with a specified time limit. Its generic syntax is:
```bash
timeout someInterval someCommand
```
This syntax translates into the following: Start a command named 'someCommand', and kill it if it still runs after specified time interval 'someInterval'. 

Again, we use command **sleep** to illustrate the use cases of **timeout** in concrete examples, but the examples below apply to any other command:
```bash
timeout 5s sleep 10s
```
This will terminate **sleep** command already after 5s, with non-zero exit status 124 (check the 'man' pages of **timeout**). The exit status is non-zero, since command failed to complete its execution within the specified time interval. By default, **timout** terminates the command execution with ```TERM``` signal, but we can send any other supported signal (check out the list with **kill -l**) by using **-s** flag, for instance:
```bash
timeout -s KILL 5s sleep 10s
```
In the case command fails by itself withing the specified time interval, then the exit status of **timeout** is the exit status of command.

Finally, in the case of successful command completion within the specified time interval, e.g.
```bash
timeout 20s sleep 10s
```
the exit status of **timeout** is 0.

As the very last remark, we indicate that unfortunatelly command **timeout** can deal only with **Linux** commands, and not for instance with **Bash** functions. 

In a completely different context, we use expression 'timing' when we want to summarize the usage of system resources by a given command. This is simply achieved with the command **time**. Its generic syntax for most cases of interest is very simple:
```bash
time someCommand
```
Here 'someCommand' is meant in a broader sense, and can be any **Linux** command, **Bash** built-in command, **Bash** function, etc. For instance:
```bash
time sleep 4s
```
gives the following expected printout:
```bash
real	0m4.002s
user	0m0.000s
sys	0m0.002s
```
However, we can use also **time** for **Bash** code snippets directly:
```bash
time for i in {1..1000}; do date; done > /dev/null 
```
produces:
```bash
real	0m1.499s
user	0m0.165s
sys	0m1.397s
```
This is clearly a great utility when the efficiency of code execution starts to matter.












### 5. Counting: **wc** <a name="counting"></a>
The number of different elements (lines, words, characters) in the file content, or in the 'stdout' of some  command, can be conveniently obtained with the command **wc** ('word count'). 

For instance:
```bash
echo "a bb ccc" | wc
```
provides the following output:
```bash
      1       3       9
```
The first entry is the number of lines (1), then the number of words (3, namely "a", "bb" and "ccc"), and finally the number of characters (9, since both the empty characters and hidden new lines also count!). Using **wc** to count the number of characters frequently leads to surprises and is not recommended because the '\n' at the end of each line is also counted, for instance:

```bash
echo | wc
```
prints 

```bash
       1       0       1
```
where the 1 character correspods to the default new line character '\n' in **echo**.

But for the counting of the number of lines and words this command behaves just as expected. Typically, we just want number of lines or number of words, when flags **-l** or **-w** can be used.

The command **wc** counts also the elements of the file. For instance, if the starting file 'wcExample.txt`' has the following content
```bash
line 1 a
line 2 b b 
line 3 c c c
```
then we can use the following:
```bash
wc -l < wcExample.txt # prints 3, total number of lines
wc -l < wcExample.txt # prints 12, total number of words (3+4+5 per line)   \>
```














### 6. Building programmatically command input: **eval** <a name="eval"></a>

We have already seen how we can programmatically provide the arguments to the commands, e.g. by referencing the content of some variables. Now we generalize this idea and illustrate how we can build the whole command input programmatically, including even the pipes. This can be achieved by using the **Bash** built-in command **eval**. In some cases, this command can be used to force additional re-evaluation of command input, if its interpretation ended up in some intermediate state.

Basically, the command **eval** enforces command-line processing once again. It's a very powerful feature, since it lets you write scripts that create command string on-the-fly and then pass them to shell for execution. By using this mechanism scripts can for instance modify their behaviour when they are already running.

We start with the following example, where we from the **date** command extract only the hour, minutes and seconds:
```bash
date | awk '{print $4}'
# prints 12:22:02
```
But now let us attempt in the **Bash** variable ```DateSimple``` to store that command input:
```bash
DateSimple="date | awk '{print $4}'"
```
If we now attempt to reference the content of ```DateSimple```, and use it directly in the same way as command input, we get an error:
```bash
$DateSimple
# date: extra operand ‘awk’
# Try 'date --help' for more information.
```
However, this saves the day:
```bash
eval $DateSimple
```
What happens above is the following: Upon expanding the content of variable ```DateSimple```, **Bash** interpreted pipe ```|``` and ```awk``` as arguments, and since it can handle only one argument (besides flags which are indicated with prepended ```-``` or ```--```), if bailed out when it hit at the second argument. When interpreting the command input, one of the very first thing **Bash** is looking for are pipes ```|```, however, since in ```$DateSimple``` there are no pipes (prior to expansion!), **Bash** stopped searching for them. Then after expanding ```$DateSimple```, **Bash** continued with the other steps in the command input interpretation, none of which includes pipes. Therefore, the pipe ```|``` ended up being interpreted as a mere argument to **date** command. This sort of problems can be fixed with **eval**, because that commands literally forces the command input re-interpretation from scratch. So after using ```eval $DateSimple```, and after expanding ```$DateSimple```, **Bash** goes from scratch through the command input interpretation, and interprets the pipe ```|``` in the correct way.

To certain degree, echo-ing the command input into the external file, and then sourcing it, achieves the same thing as **eval**, but it is much less efficient.

Finally, we mention the following frequently encountered example. We can generate sequences with brace expansion, e.g. 
```bash
echo {0..9}
```
prints 
```bash
0 1 2 3 4 5 6 7 8 9
```
But what if we want to pass low and upper end via variables? We could try:
```bash
Min=0
Max=9
echo {$Min..$Max}
```
The result is however only the following printout:
```bash
{0..9}
```
This is a nice example where **eval** saves the day again, because 
```bash
eval echo {$Min..$Max}
```
forces the re-interpretation of intermediate result ```{0..9}```, and produces the desired output:
```bash
0 1 2 3 4 5 6 7 8 9
```



