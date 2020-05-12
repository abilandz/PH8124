![](bash_logo.png)

[TBI]: <> (This is a comment)

# Lecture 6: String manipulation. Arrays. Piping (```|```). Command output and file manipulation with **sed**, **awk** and **grep** 

**Last update**: 20190606

### Table of Contents
1. [String manipulation](#string_manipulation)
2. [Arrays](#arrays)
3. [Piping (```|```)](#piping)
4. [Command output and file manipulation with **sed**, **awk** and **grep**](#sed_awk_grep)









### 1. String manipulation <a name="string_manipulation"></a>
**Bash** offers a lot of built-in functionalities to manipulate the content of variables programatically. Since the whole file content can be stored in **Bash** variable, this way we can also to certain extent solely with built-in **Bash** features manipulate the content of external files as well. However, when performance starts to matter, external programs like **sed**, **awk** or **grep**, or in extreme cases **perl**, are more suitable. 

String operators in **Bash**  are used in combination with curly bracket syntax. They are used to manipulate content of variables, typically in one of the following ways: 
1/ Remove, replace or modify portions of variable's content  that match patterns
2/ Ensure that variable exists (i.e. that it is defined and has non-null value)
3/ Set default values for variables

Generic syntax for manipulating variable's content is:
```bash
${Var/Old-pattern/New-Pattern}
```
or 
```bash
${Var//Old-pattern/New-Pattern}
```
The first version will replace only the first occurrence of the pattern within the string, while the second version will replace all occurrences. That is illustrated with the following code snippet:
```bash
Var=aaBBaa
echo ${Var/aa/CCC}
echo ${Var//aa/CCC}
```
gives:
```bash
CCCBBaa # only the first occurence of old-pattern 'aa' was replaced with new pattern 'CCC'
CCCBBCCC # all occurences of old-pattern 'aa' were replaced with new pattern 'CCC'
```
It is perfectly fine to re-define the variable on the spot to the new content, e.g.:
```bash
Var=${Var/aa/CCC}
```
Also, new and old patterns do not have to be hardwired, instead can be passed as well via variables:
```bash
Var=aaBBaa
Old=aa
New=CCC
Var=${Var/$Old/$New}
```
Curly brace syntax interprets some characters in a special way. This is illustrated with the following examples.

**Example 1:** How to get programatically the length of the string?
```bash
Var=12345678 
echo ${#Var} # prints 8 
```

**Example 2:** How to lower/upper cases of all characters in the string?
```bash
Var=aBcDeF 
echo ${Var,,} # prints 'abcdef' 
echo ${Var^^} # prints 'ABCDF' 
```

Is is also possible with this functionality to select substring from variable content, the generic syntax is:
```bash
${Var:offset:length} 
```
The above construct returns substring, starting at 'offset', and up to 'length' characters. The first character in 'Var' is at offset 0. If 'length' is ommited, it goes all the way until the end of 'Var'. If 'offset' is less than 0, then it counts from the end of 'Var'. This is illustrated with following examples:
```bash
Var=0123456789
echo ${Var:0:4} # prints '0123'
echo ${Var:5:2} # prints '56'
echo ${Var:5} # prints '56789'
echo ${Var:(-2)} # prints '89'
echo ${Var:(-3):2} # prints '78'
```
It is mandatory to embed negative offset within round braces ```(...)``` here, since without it, in this particular context **Bash** interprets negative values in a very special way---this is clarified next.

By using string operators one can determine the default value of variable under certain conditions. Most frequently one enounters the following two use cases:

1.```${Var:-defaultValue}``` => if 'Var' exists and isn't null, return it's value. Otherwise, return 'defaultValue'. Used typically to return the default value, if variable is undefined.
```bash
Var=44
echo ${Var:-100} # prints 44
```
However,
```bash
unset Var
echo ${Var:-100} # prints 100
```
This has a beautiful use case when our script or function expects user to pass an argument. Even if the user forgot to do it, we can nevertheless execute the code for some default value---it suffices to have the following example code snippet: 
```bash
Var=${1:-someDefaultValue}
```
This literally means that 'Var' is set to the first argument user has supplied to the script or function, but even if the user didn't supply anything, the code will execute by setting 'Var' to 'someDefaultValue'. 

2.```${Var:?message}``` => if 'Var' exists and isn't null, return it's value. Otherwise, prints 'Var', followed by 'messsage', and abort the current function execution (in the case it is used in script, it only prints the error message). For instance, in the body of your function you can add protection via:
```bash
local Var=${1:?first argument is missing}
```
In the case user has forgotten to provide the first argument, you bail out automatically with error message: 
```bash
bash: 1: first argument is missing
```
If you do not specify message, the default message will be produced. For instance:
```bash
Var=${3:?}
```
In the case user has forgotten to provide the third argument, this now produces the default error message: 
```bash
bash: 3: parameter null or not set
```
In the above examples ':' is optional, i.e. we could as well use: ```${Var-defaultValue}``` and ```${Var?message}```. Omitting ':' translates literally the phrase 'exists and isn't null' into 'exists'. The difference in behavior tipically concerns only the case like:
```bash
Var= # Var exists but it is NULL
echo ${Var:-44} # prints 44
echo ${Var-44} # prints nothing
```
Therefore, quite trivially we can ensure in **Bash** that all variables are used only if they were set, at least to some default values, and bail out with the error message otherwise.

When replacing old patterns with the new ones, **Bash** can handle few wildcard characters, the most important ones are:

1.```*``` : zero or more characters
2.```?``` : any single character 
3.```[ ... ]``` : character sets and ranges

Few examples illustrate their usage:
```bash
Var=1234a5678
echo ${Var/a*/TEST} # prints '1234TEST'
```
The pattern 'a*' matches any string starting with 'a' and folowed by 0 or more other characters.
```bash
Var=a1234a5678
echo ${Var//a?/TEST} # prints 'TEST234TEST678'
```
The pattern 'a?*' matches any string starting with 'a' and folowed by exactly one other character (in the above example, it matched both 'a1' and 'a5').
```bash
Var=abcde12345
echo ${Var//[b24]/TEST} # prints 'aTESTcde1TEST3TEST5'
```
The pattern '[b24]' matches any single character specified within ```[ ... ]``` (in the above example, 'b', '2' and '4' were all replaced with 'TEST').
```bash
Var=abcde12345
echo ${Var//[b-e]/TEST} # prints 'aTESTTESTTESTTEST12345'
```
The pattern '[b-e]' matches all single characters in the specified range within ```[ ... ]``` (in the above example, 'b', 'c', 'd' and 'e', i.e. all characters in the range 'b-4', were all replaced with 'TEST').

The real power of wildcards come into play when the above functionalities are being combined:
```bash
Var=a1b2c3d4e5
echo ${Var//[b-d]?/TEST} # prints 'a1TESTTESTTESTe5'
```
The pattern '[b-d]?' matches all single characters in the specified range 'b-d' followed up by exactly one other character (in the above example, 'b2', 'c3' and 'd4', were all replaced with 'TEST').
```bash
Var=acebfd11g
echo ${Var^^[c-f]} # prints 'aCEbFD11g'
```
The pattern '^^[c-f]' will capitalize all single characters, but only in the specified range 'c-f', therefore 'c', 'd', 'e' and 'f' in the above example.

 





### 2. Arrays <a name="arrays"></a>

**Bash** also supports arrays, i.e. variables containing multiple values, which can be of same type or of different type. In **Bash** array index starts with zero, and there is no limit to the size of an array. There are few ways in which array elements can be declared, the quickest one is to use round braces ```( ... )```. This is illustrated in the following code snippet:
```bash
SomeArray=( 5 a ccc 44 )
```
The array elements are separated with one or more empty characters, elements can be any string, not necessarily of the same type. To reference the content of particular array element, we use again curly brace notation ```${array-name[index]}```, for instance:
```bash
echo ${SomeArray[0]} # prints '5'
echo ${SomeArray[2]} # prints 'ccc' 
```
To get programatically all array entries, we can use ```${array-name[*]}``` syntax:
```bash
echo ${SomeArray[*]} # prints '5 a ccc 44'
```
This means that we can very conveniently loop over all array entries with:
```bash
for Entry in  ${SomeArray[*]}; do
 echo $Entry
done
```
Total number of elements in an array is given by the syntax ```${#array-name[*]}```, i.e..
```bash
echo ${#SomeArray[*]} # prints '4'
```
We can assign value directly:
```bash
SomeArray[2]=ddd
```
Now if we print all elements, we get:
```bash
echo ${SomeArray[*]} # prints '5 a ddd 44'
```
In order to remove the element of an array, we need to explicitly use the key word **unset**:
```bash
unset SomeArray[2]
echo ${SomeArray[*]} # prints '5 a 44'
echo ${#SomeArray[*]} # prints '3', i.e. the size was also reset
```
On the other hand, unsetting array alement with:
```bash
SomeArray[2]= # WRONG!! The total size of array is not reset
```
is wrong, since the total size of array was not reset, i.e. this particular element is still counted, only it has now NULL content. 
The array index works also backwards, i.e. the last array element is:
```bash
echo ${SomeArray[-1]}
```
the penultimate array entry is:
```bash
echo ${SomeArray[-2]}
```
and so on. To append directly to the already existing array a new element, we can use programatically always the following trick:
```bash
SomeArray[${#SomeArray[*]}]=12345 # appending a new element
```

The power and flexibility of arrays come from the fact that at declaration within ```( ... )``` a lot of other **Bash** functionalities are being supported, for instance the command substitution operator ```$( ... )``` and brace expansion ```{ ... }```. That in particular means that we can effortlessly store the entire output of command into an array, and then do some manipulation element-by-element. 

**Example 1:** Count the number of files in the specified directory using arrays. 

The solution is very simple:
```bash
SomeDir=$PWD # using for simplicity the current working directory. but it can be anything esle
Array=( $(ls $SomeDir) )
echo "Number of files: ${#Array[*]}"
```

**Example 2:** How to append one array to another, without using loops?

The solution is:
```
Array1=( 1 2 3 )
Array2=( a b c )
NewArray=( ${Array1[*]} ${Array2[*]} )
echo ${NewArray[*]} # prints '1 2 3 a b c'
```

**Example 3:** Using brace expansion at array declaration.
```bash
SomeArray=( file_{1..3}.{pdf,eps} )
echo ${SomeArray[*]} # print all array elements
```
The printout is:
```bash
file_1.pdf file_1.eps file_2.pdf file_2.eps file_3.pdf file_3.eps
```

**Example 4:** How can we catch user's input directy into an array?

We have already seen that by using **read** command we can catch user's input, but if we want to store the input in few different variables, that quickly become inconvenient. And quite frequently we cannot really foresee the length of user's input. For instance, how to handle the user's reply to the question: "Which countries you visited so far?" That can be solved elegantly with arrays:
```bash
read -p "Which countries you visited so far? " -a Countries
```
By using the flag **-a** for command **read**, we have indicated that whatever user types, it will be stored in an array (in the above example named 'Countries'). Then we can immediately write for instance:
```bash
echo "Number of countries is: ${#Countries[*]}"
echo "The first country is: ${Countries[0]}"
echo "The last country is: ${Countries[-1]}"
```
Alternative, one line lengthier solution, is:
```bash
read -p "Which countries you visited so far? "
Countries=( $REPLY ) # yes, we can also initialize the content of array with the content of variable
```
Multi-dimensional (associative) arrays are rarely used in **Bash**, but nevertheless they are supported. They need to be declared explicitly with **Bash** built-in command **declare** and flag **-A**, e.g.
```bash
declare -A SomeArray
``` 
After such declaration, **Bash** understands how to cope with the following syntax:
```bash
SomeArray[1,2,3]=a
SomeArray[2,3,1]=bb
```
To reference the content, we use the syntax:
```bash
echo ${SomeArray[1,2,3]} # prints 'a' 
echo ${SomeArray[2,3,1]} # prints 'bb'
```













### 3. Piping <a name="piping"></a>
We have already seen that commands can take their input directly from the user or from files. But in general, one command can take automatically the output of another command as its input. This mechanism is called _piping_ and it's very generic **Linux** concept. 

In order to redirect output of one command as an input to another, we use operator ```|``` ('pipe'), schematically as:

```bash
firstCommand | secondCommand
```
It is possible to chain this way multiple commands::

```bash
firstCommand | secondCommand | thirdCommand | ...
```

The power of _piping_ is best illustrated in the combination with the three commands **sed**, **awk** and **grep** for text parsing and manipulation, which we will cover in the next section.  Usage of pipe ```|``` eliminates the need for making temporary files to redirect and store the output of one command, and then supply that temporay file as an input to another command.  We can with pipes very easily make our own version of already existing commands (e.g. by slighly changing the output format and wrapping up the implementation in some **Bash** function). We now provide few frequently use cases of piping.

We have already seen that **Bash** supports directly only integer arithmetics with the construct ```(( ... ))```. Floating point arithmetics in **Bash** can be done by piping the desired expression into the external programm **bc** ('basic calculator'). 

**Example 1:** How would you divide 10/7 at the precision of 30 significant digits? 
Solution is given by the following code snippet:
```bash
echo "scale=30; 10/7" | bc
```
The output is:
```
1.428571428571428571428571428571
```
The key word **scale** sets the precision. For sophisticated cases, i.e. if you want to use special functions, etc, use: ```bc -l``` (flag **-l** loads additionally the heavy mathematical libraries). If the scale is not specified, it is defaulted to 1 when ```bc``` is called, and to 20 when ```bc -l``` is called.

The math library of **bc** defines the following example functions:
```bash
s(x) : The sine of x, x is in radians.
c(x) : The cosine of x, x is in radians.
a(x) : The arctangent of x, arctangent returns radians.
l(x) : The natural logarithm of x.
e(x) : The exponential function of raising e to the value x.
j(n,x) : The bessel function of integer order n of x.
```

**Example 2:** How would you calculate ```e^2``` to the precision of 20 significant digits? 
```bash
echo "e(2)" | bc -l # prints '7.38905609893065022723'
```

**Example 3:** What do you need to do if you want to see _stdout_ of your command on the screen during execution but in parallel to be redirected to some file (very reasonanle requirement in fact because you typically want to see what your command is doing, but also eventually to trace back all execution history)?

This can be achieved with the **tee** command, schematically:
```bash
someCommand | tee someFile.log 
```
For instance:
```bash
date | tee date.log
```
prints the current time on the screen, but also simultaneously dumps it in the file ```date.log``` (check its content with ```cat date.log```).

The command **tee** writes simulteneously its input to _stdout_ (screen) and redirects it to the files. By default **tee** overwrites the content of file, if we want to append instead, use the following version:
```bash
someCommand | tee -a someFile.log 
```

In the same spirit, you can keep the execution log of any script, functions, code block ```{ ... }```, loops, etc. This way, you can always recover in great detail what your code was doing throughout its execution.











### 4. Command output and file manipulation with **sed**, **awk** and **grep** <a name="sed_awk_grep"></a>

Any text can be parsed, filtered, modifed programmatically, etc., directly whether it is command output, or sitting in some physical file, with the three great commands: **grep**, **awk** and **sed**. Combining functionalities of all three of them, gives you a lot of power with programmatic text manipulation. Their usage is best learned from concrete examples.

The command **grep** ('Globally search a Regular Expression and Print') is used to filter out from the command output or from the physical file the lines containing the certain pattern.

**Example 1:** Edit in **nano** the following file _grepExample.txt_:
```bash
TEST Test test 11test test22
test TEST Test 11test test22
TeST1 TEST1 TESt1 TEST1 TEST1
test TEST Test 11test test
TeST2 TEST2 TEsT2 TEST2 tEST2
```

And then apply on it the following **grep** examples:

```bash
# print all lines in the file containing pattern "test" 
grep "test" grepExample.txt 

# print all lines in the file containing pattern "test", and the numbers of those lines: 
grep -n "test" grepExample.txt 

# inverse search: print all lines in the file which does NOT contain the pattern "test" 
grep -v "test" grepExample.txt

# case insensitive search: print all lines in the file which contain the pattern "test" or "TEST" or "tEsT", etc. 
grep -i "test" grepExample.txt

# beginning of the line: print all lines in the file which contain the pattern "test" ONLY at the beginning of the line 
grep "^test" grepExample.txt # => use anchor ^

# end of the line: print all lines in the file which contain the pattern "test" ONLY at the end of the line 
grep "test$" grepExample.txt # => use anchor $

# word begins with the pattern: print all lines in the file which contain the word which begins with the pattern "test"
grep "\<test" grepExample.txt

# word ends with the pattern: print all lines in the file which contain the word which ends with the pattern "test"
grep "test\>" grepExample.txt

# exact match: print all lines in the file which contain the word which is exactly the same to the pattern "test"
grep "\<test\>" grepExample.txt 
grep -w "test" grepExample.txt 

# OR: print all lines in the file which contain either the pattern "11test" or "test22"
grep "11test\|test22" grepExample.txt # => within grep, OR is represented with \| 

# AND: print all lines in the file which contain both the pattern "11test" and "test22"
grep "11test" grepExample.txt | grep "test22" # => within grep, there is no built-in AND operator, but piping saves the day 

# quiet grep: if you are just interested if a file contains the certain pattern, without actual printout
grep -q "11test" grepExample.txt && echo "yes, file contains pattern 11test" || echo "no, file doesn't contain pattern 11test"

# filter out of lines with the pattern "test" to another file:
grep "test" grepExample.txt 1> grepOutput.log
cat grepOutput.log
```

Just like we can grep the content of the files, we can equivalently handle the command output---this is the prime use case of pipes.
 
**Example 2:** How to select in the current directory only the files whose names begin with example pattern 'ce' and ends with '.dat'? The content of directory is:
```bash
array.sh   be3.dat  be8.dat  ce1.log  ce4.dat  ce6.log  ce9.dat
array.sh~  be4.dat  be9.dat  ce2.dat  ce4.log  ce7.dat  ce9.log
be0.dat    be5.dat  ce0.dat  ce2.log  ce5.dat  ce7.log  grepExample.txt
be1.dat    be6.dat  ce0.log  ce3.dat  ce5.log  ce8.dat  test.sh
be2.dat    be7.dat  ce1.dat  ce3.log  ce6.dat  ce8.log  test.sh~
```
The first part of solution is:
```bash
ls | grep "^ce*"
```
This will provide the list of all files in your current directory with **ls** command, then pipe it to **grep** which will select only the files beginning (note the usage of anchor ```^``` for beginning!) with pattern 'ce'. It is perfectly reasonable to filter further by chaining another pipe, so the final solution is:
```bash
ls | grep "^ce*" | grep ".dat$"
```
Now we have used the anchor ```$```, since we are interested in ending. The final output is:
```bash
ce0.dat
ce1.dat
ce2.dat
ce3.dat
ce4.dat
ce5.dat
ce6.dat
ce7.dat
ce8.dat
ce9.dat
```

Now we move to **awk** (named after the initials of its authors: Aho, Weinberg and Kernighan), which is a programming language by itself, designed for text processing. One can easily teach the whole semester only about **awk**, here we will cover only its most important functionalities which are not available as built-in **Bash** functionalites. The frequently used comment about **awk** is that its syntax and usage is awkward. Nevertheless, in lot of cases of interest it provides the best and most elegant solution.

**Awk** breaks each line of input passed to it into fields, separated by default with one or more empty characters. After that, **awk** parses the input and operates on each separate field. Just like with the **grep** command, **awk** can take its input either from the external file, or from the output of another command via pipe. For instance, if a certain command has produced an output which consists of column-wise entries separated with one or more empty characters, we can handle each field programmatically. For instance:
```bash
date
date | awk '{print $4}'
date | awk '{print $6}'
date | awk '{print $4, "some text", $6}'
```
The output is:
```bash
Do 6. Jun 09:21:02 CEST 2019 # the full command output
09:21:05 # taking only hour, minutes and seconds, this is the 4th field
2019 # taking only the year, this is the 6th field
09:21:05 some text 2019 # modifying the command output in user-specific way
```
In order to get the total number of fields, we can use the **awk** built-in variable **NF**
```bash
date | awk '{print NF}' # prints 6, since there are that many distinct fields in 'date' output
```
The entry from the last column can be achieved directly by referencing that variable:
```bash
date | awk '{print $NF}' # prints 2019
```
while the entry from penultimate field can be obtained directly with:
```bash
date | awk '{print $(NF-1)}' # prints CEST
```
and so on. 

But what if we want to parse the command output even more differentially, i.e.  what if we want to extract programmatically from the output of **date** command only the minutes? In order to achieve that, in general we need to change the field separator in **awk** to some non-default value. This is achieved by manipulating the **awk** built-in variable **FS**. In order to set the field separator **FS** to some non-default value,  we use schematically the following syntax:
```bash
awk 'BEGIN {FS="some-new-single-character-field-separator"} ... '
```
For instance, if we want to use colon ```:``` as a field separator in **awk**, we would use:
```bash
awk 'BEGIN {FS=":"} ... '
```
So to extract only the minutes from the output of **date** command, we would use the following code snippet:
```bash
date
date | awk '{print $4}' | awk 'BEGIN {FS=":"}{print $2}'
```
The output is:
```bash
Do 6. Jun 09:44:59 CEST 2019 # the full command output
44 # minutes
```
What happened above is literally the following:
1. **date** command produced the output ```Do 6. Jun 09:44:59 CEST 2019```
2. that output was piped to **awk** command, which extracted the 4th field, taking into account that the default field separator is one or more empty characters. The results is ```09:44:59```
3. in the 2nd pipe the stream ```09:44:59``` was fed again to **awk** command, but now with the non-default field separator ```:``` . With respect to that field separator, the 2nd field is minutes. 
 
As a rule of thumb, fields separators in **awk** shall be always single characters---composite field separators can lead to some inconsistent behaviour among different **awk** versions (e.g. **gawk**, **mawk**, etc.).  Different  single characters can be treated as field separators in one go simultaneously just embed them all within ```[ ... ]```, for instance:
```bash
echo "1+10:44+1000:123" | awk 'BEGIN{FS="[+:]"} {print $3}' 
```
The output is
```bash
44
``` 
The main limitation of **awk** when used within **Bash** scripts is that it cannot swallow directly the **Bash** variables, i.e. we need to initialize first some internal **awk** variables with the content of  **Bash** variables, before we can use them within **awk**.

Finally, there is **sed** ('Stream Editor'), a non-interactive text file editor. It parses the command output or file content line-by-line, and performs specified operations on them. We illustrate its usage also with some basic examples.

**Example 1:** How to insert a new 2nd line of text in the already existing file ```sedTest.dat```, which has the following content:
```bash
line 1
line 2
line 3
line 4
```
The solution is:
```bash
sed "2i Some text" sedTest.dat
``` 
This will insert in the second line (the meaning of '2i' specifier) of the file ```sedTest.dat``` the text _"Some text"_. The original file is not modified, only the **sed** output stream. The output on the screen is:
```bash
line 1
Some text
line 2
line 3
line 4
```
In the case you want the original file to be modified on the spot, you need to use flag ```-i``` ('in-place edit') for **sed** :
```bash
sed -i "2i Some text" sedTest.dat
```
This will in the second line of the file ```sedTest.dat``` instert the text _"Some text"_. The original file is modified, without backup. Clearly, this can be potentially dangerous, as once the original file is overwritten, there is no way back. To prevent that, we can automatically create the backup of original file by using the flag ```-i.backup```:
```bash
sed -i.backup "2i Some text" sedTest.dat
```
This will in the second line of the file ```sedTest.dat``` instert the text _"Some text"_. The original file is modified, but also the backup of original file was created, in the new file ```sedTest.dat.backup```

Besides inserting new lines in the file, **sed** can also delete lines from a file programmatically. For instance, if we want to delete the 4th line, we use the following syntax: 
```bash
sed "4d" sedTest.dat
```
This will delete the 4th ('4d' specifier) line in the file ```sedTest.dat```. We can also specify the line ranges for deletion, for instance:
```bash
sed "2,4d" sedTest.dat
```
This will delete the 2nd, 3rd and 4th lines in the file ```sedTest.dat```.

Finally, we also illustrate how to replace one pattern in the file with another. This is achieved with the following generic syntax:
```bash
sed "s/firstPattern/secondPattern/" someFile
```
This will substitute ('s' specifier) in each line of file ```sedTest.dat``` only the first occurence of ```firstPattern``` with ```secondPattern```. On the other hand, if we want to replace all occurences, we need to use the following, slightly modified syntax: 
```bash
sed "s/firstPattern/secondPattern/g" someFile
```
Note the additional specifier 'g' for 'global' at the end of expression. The nice thing about **sed** is that it can interpret **Bash** variables directly (**awk** for instance cannot), i.e. it is perfectly feasible in your script to have something like:
```bash
Before=SomeOldPatern
After=SomeNewPatern
sed "s/${Before}/${After}/" someFile
```
With **sed** we can also trivially modify the output stream of some command:
```bash
date
date | sed "s/Do/Thursday/"
```
The output is:
```bash
Do 6. Jun 13:40:15 CEST 2019
Thursday 6. Jun 13:40:15 CEST 2019
```
Finally, **sed** provides full support for pattern matching via regular expressions.





