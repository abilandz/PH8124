![](bash_logo.png)

# Lecture 6: String manipulation. Arrays. Piping (```|```). Command output and file manipulation with **sed**, **awk** and **grep** 

**Last update**: 20200530

### Table of Contents
1. [String manipulation](#string_manipulation)
2. [Arrays](#arrays)
3. [Piping: ```|```](#piping)
4. [Command output and file manipulation with **sed**, **awk** and **grep**](#sed_awk_grep)









### 1. String manipulation <a name="string_manipulation"></a>
**Bash** offers a lot of built-in functionalities to manipulate the content of variables programmatically. Since the content of an external file can be stored in a **Bash** variable, we can to a certain extent solely with built-in **Bash** features manipulate the content of external files as well. However, performance starts to matter typically for large files, when **Linux** core utilities **sed**, **awk** and/or **grep** are more suitable. For very large files, when performance becomes critical, one needs to use the high-level programming languages, like **perl**. 

String operators in **Bash** can be used only in combination with curly brace syntax, ```${Var}```, when the content of a variable is referenced. String operators are used to manipulate the content of variables, typically in one of the following ways:     

1. Remove, replace or modify a portion of variable's content that matches some patterns   
2. Ensure that variable exists (i.e. that it is defined and has a non-zero value)   
3. Set the default value for a variable   

The generic syntax for manipulating the content of the variable is:
```bash
${Var/old-pattern/new-pattern}
```
or 
```bash
${Var//old-pattern/new-pattern}
```
The first version will replace only the first occurrence of the pattern 'old-pattern' with 'new-pattern' within the string which is stored in the variable 'Var', while the second version will replace all occurrences. This is illustrated with the following code snippet:
```bash
Var=aaBBaa
echo ${Var/aa/CCC}
echo ${Var//aa/CCC}
```
which prints:
```bash
CCCBBaa
CCCBBCCC
```
It is perfectly fine to re-define the variable on the spot with the new content:
```bash
Var=${Var/aa/CCC}
```
The new and old patterns do not have to be hardwired, instead, they can be specified via variables:
```bash
Var=aaBBaa
Old=aa
New=CCC
Var=${Var/$Old/$New}
```
The curly-brace syntax interprets some characters in a special way. This is illustrated with the following examples.

**Example 1:** How to get programmatically the length of the string?

```bash
Var=1a3b56F8 
echo ${#Var} # prints 8 
```

**Example 2:** How to lower/upper cases of all characters in the string?

```bash
Var=aBcDeF 
echo ${Var,,} # prints 'abcdef' 
echo ${Var^^} # prints 'ABCDF' 
```

It is also possible with the curly brace syntax to select substring from variable content, with the following generic syntax is:
```bash
${Var:offset:length} 
```
The above construct returns substring, starting at 'offset', and up to 'length' characters. The first character in the content of variable 'Var' is at the offset 0. If 'length' is omitted, it goes all the way until the end of 'Var'. If 'offset' is less than 0, then it counts from the end of 'Var'. This is illustrated with the following examples:
```bash
Var=0123456789
echo ${Var:0:4} # prints '0123'
echo ${Var:5:2} # prints '56'
echo ${Var:5} # prints '56789'
echo ${Var:(-2)} # prints '89'
echo ${Var:(-3):2} # prints '78'
```
It is mandatory to embed negative offset within round braces ```( ... )``` in the above example, since otherwise **Bash** interprets negative integers after colon ```:``` in such a context in a very special way---this is clarified next.

By using string operators one can set the default value of a variable. Most frequently, one encounters the following two use cases:  

1. ```${Var:-defaultValue}``` : if 'Var' exists and it is not null, return its value. Otherwise, return the hardwired 'defaultValue'. This is basically protection that variable always has some content. For instance:

   ```bash
    Var=44
    echo ${Var:-100} # prints 44
   ```
However:

   ```bash
   unset Var
   echo ${Var:-100} # prints 100
   ```

   This syntax has a very important use case when a script or a function expects the user to supply an argument. Even if the user forgot to do it, we can nevertheless execute the code for some default and meaningful value of that argument. For instance:
   
   ```bash
   Var=${1:-defaultValue}
   ```

   This literally means that 'Var' is set to the first argument the user has supplied to a script or a function, but even if the user forgot to do it, the code can still execute by setting 'Var' to 'defaultValue'.   

2. ```${Var:?someMessage}``` : if 'Var' exists and it is not null, return its value. Otherwise, prints 'Var', followed by hardwired text 'someMessage', and abort the current execution of a function (in the case this syntax is used in a script, it only prints the error message). For instance, in the body of your function you can add protection via:

   ```bash
   function myFunction
   {
    local Var=${1:?first argument is missing}
    ... some code ... 
   } 
   ```

   In case the user has forgotten to provide the first argument, your function will terminate automatically with the error message: 
   ```bash
   myFunction
   bash: 1: first argument is missing
   ```
   If you do not specify the message, the default message will be produced. For instance:

   ```bash
   unset someVariable 
   Var=${someVariable:?}
   ```
   will produce the following error message:
   ```bash
   -bash: someVariable: parameter null or not set
   ```
   

In both of these examples we have used colon ```:``` within the curly braces, but this is optional. However, if we omit the colon ```:``` and use instead the syntax ```${Var-defaultValue}``` and ```${Var?someMessage}```, the meaning is slightly different: the previous phrase 'exists and it is not null' translates now only into 'exists'. This difference concerns cases like this:

```bash
Var= # Var exists but it is NULL
echo ${Var:-44} # prints 44
echo ${Var-44} # prints nothing
```
When replacing old patterns with the new ones, **Bash** can handle a few wildcard characters. The most important wildcards are:  

1.  ```*``` : zero or more characters    
2.  ```?``` : any single character     
3.  ```[ ... ]``` : character sets and ranges

Their usage is best illustrated with a few concrete examples:
```bash
Var=1234a5678
echo ${Var/a*/TEST} # prints '1234TEST'
```
Here the pattern with the wildcard, 'a*', matches any string starting with 'a' and followed by 0 or more other characters.
```bash
Var=a1234a5678
echo ${Var//a?/TEST} # prints 'TEST234TEST678'
```
The pattern with the wildcard 'a?' matches a string starting with the character 'a' and followed by exactly one other character (in the above example, it matched both 'a1' and 'a5', which were both replaced, due to ```//``` specification within curly braces, into a new pattern 'TEST').
```bash
Var=abcde12345
echo ${Var//[b24]/TEST} # prints 'aTESTcde1TEST3TEST5'
```
The pattern '[b24]' matches any single character specified within ```[ ... ]``` (in the above example, 'b', '2' and '4' were all replaced with 'TEST').
```bash
Var=abcde12345
echo ${Var//[b-e]/TEST} # prints 'aTESTTESTTESTTEST12345'
```
The pattern '[b-e]' matches all single characters in the specified range within ```[ ... ]``` (in the above example, 'b', 'c', 'd' and 'e', i.e. all characters in the range 'b-e' were all replaced with the new pattern 'TEST').

The real power of wildcards is manifested when they are combined:
```bash
Var=a1b2c3d4e5
echo ${Var//[b-d]?/TEST} # prints 'a1TESTTESTTESTe5'
```
The pattern '[b-d]?' matches all single characters in the specified range 'b-d' followed up by exactly one other character (in the above example, 'b2', 'c3' and 'd4' were all replaced with 'TEST').
```bash
Var=acebfd11g
echo ${Var^^[c-f]} # prints 'aCEbFD11g'
```
The pattern '^^[c-f]' will capitalize all single characters, but only in the specified range 'c-f', therefore only 'c', 'd', 'e' and 'f' in the above example get capitalized. 

 







### 2. Arrays <a name="arrays"></a>

**Bash** also supports arrays, i.e. variables containing multiple values. Since all variables in **Bash** by default are strings, you can store in the very same array integers, text, etc. Array index in **Bash** starts with zero, and there is no limit to the size of an array. There are a few ways in which an array can be initialized with its elements --- the quickest one is to use the round braces ```( ... )```. This syntax is illustrated with the following code snippet:

```bash
SomeArray=( 5 a ccc 44 )
```
Array elements are separated with one or more empty characters. To reference the content of a particular array element, we use again the curly brace notation ```${ArrayName[index]}```. For instance, for the above example we have:
```bash
echo ${SomeArray[0]} # prints '5'
echo ${SomeArray[2]} # prints 'ccc' 
```
To get programmatically all array entries, we can use ```${ArrayName[*]}``` or ```${ArrayName[@]}``` syntax, for instance:
```bash
echo ${SomeArray[*]} # prints '5 a ccc 44'
```
The difference between ```${ArrayName[*]}``` or ```${ArrayName[@]}``` syntax matters only when used within double quotes, and the explanation is the same as for a difference between ```"$*"``` and "$@" when referring to the list of positional parameters (see Lecture #2).

This means that we can very conveniently loop over all array entries with:

```bash
for Entry in ${SomeArray[*]}; do
 echo $Entry
done
```
The printout is:

```bash
5
a
ccc
44
```

The total number of elements in an array is given by the syntax ```${#ArrayName[*]}```:

```bash
echo ${#SomeArray[*]} # prints '4'
```
We can set the value of a particular array element directly:
```bash
SomeArray[2]=ddd
```
Now if we print all elements, the initial 3rd element 'ccc' was replaced with the new value 'ddd', and we get:
```bash
echo ${SomeArray[*]} # prints '5 a ddd 44'
```
In order to remove a particular element of an array, we need to explicitly use the keyword **unset**. This way, the length of an array and all indices are automatically recalculated:
```bash
unset SomeArray[2]
echo ${SomeArray[*]} # prints '5 a 44'
echo ${#SomeArray[*]} # prints '3', the array was resized
```
On the other hand, unsetting the array element with:
```bash
SomeArray[2]= # WRONG!!
```
is wrong, since the total size of an array was not reset, i.e. this particular element is still counted as a part of an array, only it has now NULL content. 

The whole array can be reset either with

```bash
unset SomeArray
```

or 

```bash
SomeArray=()
```

The array index works also backward. The last array element is:

```bash
echo ${SomeArray[-1]}
```
the penultimate array entry is:
```bash
echo ${SomeArray[-2]}
```
and so on. To append directly to the already existing array a new element, we can use programmatically the following code snippet:
```bash
SomeArray[${#SomeArray[*]}]=SomeValue
```

The above syntax works, because array indexing starts from 0 and ends with N-1, where N is the total number of array elements. Since ```${#SomeArray[*]}``` gives the total number of array elements N, the above syntax just appends the new N-th entry.

Quite frequently, we need to prepend or append the same string to all array elements. This can be achieved  elegantly with the following syntax:

```bash
SomeArray=${SomeArray[*]/#/SomePattern} # prepend
SomeArray=${SomeArray[*]/%/SomePattern} # append
```

**Example 1:** We have the following starting array which just contains some file names:

```bash
Files=( file_0 file_1 file_2 )
```

How to append to all file names the same file extension '.dat'? How to prepend to all file names the same string 'some_'?

The solution to the first question is:

```bash
Files=( ${Files[*]/%/.dat} )
```

In the above code snippet, we have first appended (operator ```%```) to all array elements the same extension '.dat', and immediately redefined the array to the new content. The array elements are now:

```bash
echo ${Files[*]}
file_0.dat file_1.dat file_2.dat
```

The solution to the second question is:
```bash
Files=( ${Files[*]/#/some_} )
```
In the above code snippet, we have first prepended (operator ```#```) to all array elements the same string 'some_' , and we have then redefined the array to the new content, so the array elements are now :

```bash
echo ${Files[*]}
some_file_0.dat some_file_1.dat some_file_2.dat some_file_3.dat
```

By using this functionality, we can prepend programmatically to all file names in a given directory the absolute path to that directory --- we just need to prepend the pattern **${PWD}/**. 

The power and flexibility of arrays come from the fact that at array declaration within ```( ... )``` a lot of other **Bash** functionalities are supported, for instance, the command substitution operator ```$( ... )``` and brace expansion ```{ ... }```. That in particular means that we can effortlessly store the entire output of a command into an array, and then do some manipulation element-by-element. 

**Example 2:** Count the number of words in an external file using arrays. 

The solution is very simple and elegant:
```bash
FileContent=$(< SomeFile)
SomeArray=( ${FileContent} )
echo "Number of words: ${#SomeArray[*]}"
```

In the first line we have stored the content of an external file ```SomeFile``` into variable **FileContent**, and then just defined the array elements by referencing its content. The empty characters which separate the words in the file, now separate the array elements in the definition. 

At the expense of becoming a bit cryptic, the above solution can be condensed even further:

```bash
SomeArray=( $(< SomeFile) )
echo "Number of words: ${#SomeArray[*]}"
```

**Example 3:** How to append entries of one array to another, without using loops?

The solution is:
```bash
Array_1=( 1 2 3 )
Array_2=( a b c d )
NewArray=( ${Array_1[*]} ${Array_2[*]} )
echo ${NewArray[*]} # prints '1 2 3 a b c d'
```

**Example 4:** Using brace expansion at array declaration.

```bash
SomeArray=( file_{0..3}.{pdf,eps} )
echo ${SomeArray[*]}
```
The printout is:
```bash
file_0.pdf file_0.eps file_1.pdf file_1.eps file_2.pdf file_2.eps file_3.pdf file_3.eps
```

**Example 5:** How to store the output of some command in array?

```bash
SomeArray=( $(date) )
```

We can now extract from the output of **date** only a particular entry:

```bash
echo ${SomeArray[*]}
# prints: 'Fri May 29 16:24:25 CEST 2020'
echo "Current month: ${SomeArray[1]}"
# prints: 'Current month: May'
echo "Current time: ${SomeArray[3]}"
# prints: 'Current time: 16:24:25'
```

**Example 6:** How can we catch the user's input directly into an array?

We have already seen that by using **read** command we can catch the user's input, but if we want to store the input in a few different variables, that quickly becomes inconvenient. And quite frequently, we cannot foresee the length of the user's input. For instance, how to handle the user's reply to the question: "Which countries you visited?" That can be solved elegantly with arrays:
```bash
read -p "Which countries you visited? " -a Countries
```
By using the flag **-a** for command **read**, we have indicated that whatever user types, it will be split according to the empty character, i.e. the default input field separator into words, and then each word is stored as a separate element in an array (in the above example, that arrayed is named 'Countries'). 

Then we can immediately write for instance:

```bash
echo "Number of countries is: ${#Countries[*]}"
echo "The first country is: ${Countries[0]}"
echo "The last country is: ${Countries[-1]}"
```
But what if the user visited New Zealand or Northern Ireland? Since these countries contain an empty character in their names, the code above clearly cannot correctly handle these cases. In general, the problems of this type are solved by temporarily changing the default input field separator. The default input field separator is stored in the environment variable **IFS**, and a lot of **Linux** commands rely on its content. We can proceed in the following schematic way:

``` bash
DefaultIFS="$IFS" # save default
IFS=somethingNew
... some code with new IFS ...
IFS="$DefaultIFS" # revert back
```

This is the frequently encountered case in practice, when a certain variable needs to be set only during the command execution. There exists a specialized syntax applicable to cover such uses cases:

```bash
SomeVariable=someValue SomeCommand
```

Note that there is no semicolon ```;``` between variable definition and command execution, this way the new definition of variable **SomeVariable** is visible only during the execution of **SomeCommand**. As soon as command terminates, **SomeVariable** gets automatically reset to its default value (if any).

The final solution for our example is therefore:

```bash
IFS=',' read -p "List (comma separated) countries you visited: " -a Countries
```

This way, the input field separator will be comma ```,``` but only during the execution of **read**.

Now if the User replied 'New Zealand,Northern Ireland' we have that:

```bash
echo ${Countries[0]}
# prints: New Zealand
echo ${Countries[1]}
# prints: Northern Ireland
```

As the final remark on arrays, we indicate that multidimensional (associative) arrays are rarely used in **Bash**, but nevertheless, they are supported. They need to be declared explicitly with **Bash** built-in command **declare** and flag **-A**:

```bash
declare -A SomeArray
```
After such declaration, **Bash** understands how to cope with the following syntax:
```bash
SomeArray[1,2,3]=a
SomeArray[2,3,1]=bb
```
To reference the content of elements in multidimensional arrays, we use:
```bash
echo ${SomeArray[1,2,3]} # prints 'a' 
echo ${SomeArray[2,3,1]} # prints 'bb'
```

The indices do not have to be hardwired --- index of **Bash** arrays can be any expression that evaluates to 0 or a positive integer. 











### 3. Piping: ```|``` <a name="piping"></a>
We have already seen that commands can take their input directly from the user or from files. But in general, one command can take automatically the output of another command as its input. This mechanism is called _piping_ and it's very generic **Linux** concept. 

In order to redirect output of one command as an input to another, we use operator ```|``` ('pipe'), schematically as:

```bash
firstCommand | secondCommand
```
It is possible to chain this way multiple commands::

```bash
firstCommand | secondCommand | thirdCommand | ...
```

The power of _piping_ is best illustrated in the combination with the three commands **sed**, **awk** and **grep** for text parsing and manipulation, which we will cover in the next section.  Usage of pipe ```|``` eliminates the need for making temporary files to redirect and store the output of one command, and then supply that temporary file as an input to another command.  We can with pipes very easily make our own version of already existing commands (e.g. by slightly changing the output format and wrapping up the implementation in some **Bash** function). We now provide few frequently use cases of piping.

We have already seen that **Bash** supports directly only integer arithmetic with the construct ```(( ... ))```. Floating point arithmetic in **Bash** can be done by piping the desired expression into the external program **bc** ('basic calculator'). 

**Example 1:** How would you divide 10/7 at the precision of 30 significant digits? 
Solution is given by the following code snippet:
```bash
echo "scale=30; 10/7" | bc
```
The output is:
```bash
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

**Example 3:** What do you need to do if you want to see _stdout_ of your command on the screen during execution but in parallel to be redirected to some file (very reasonable requirement in fact because you typically want to see what your command is doing, but also eventually to trace back all execution history)?

This can be achieved with the **tee** command, schematically:
```bash
someCommand | tee someFile.log 
```
For instance:
```bash
date | tee date.log
```
prints the current time on the screen, but also simultaneously dumps it in the file ```date.log``` (check its content with ```cat date.log```).

The command **tee** writes simultaneously its input to _stdout_ (screen) and redirects it to the files. By default **tee** overwrites the content of file, if we want to append instead, use the following version:
```bash
someCommand | tee -a someFile.log 
```

In the same spirit, you can keep the execution log of any script, functions, code block ```{ ... }```, loops, etc. This way, you can always recover in great detail what your code was doing throughout its execution.











### 4. Command output and file manipulation with **sed**, **awk** and **grep** <a name="sed_awk_grep"></a>

Any text can be parsed, filtered, modified programmatically, etc., directly whether it is command output, or sitting in some physical file, with the three great commands: **grep**, **awk** and **sed**. Combining functionalities of all three of them, gives you a lot of power with programmatic text manipulation. Their usage is best learned from concrete examples.

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





