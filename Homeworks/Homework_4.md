![](../Common_Figures/LinuxBashROOT_logos.png)

# Filtering and reformatting the file content programmatically

**Last update:** 20230602

**Challenge #1**: Develop a **Bash** function named ```OldestFile``` which takes as arguments a list of files. The function does the following:  

1. terminates with the error exit status 1 if the user called the function without supplying any arguments  
2. terminates with the error exit status 2 if some file which the user has supplied does not exist (and it prints then the warning and the name of that problematic file)   
3. prints the name of the file which has not been modified for the longest period, and exits with success exit status 0   

The user would like to use your function in the following schematic way:

```bash
OldestFile file1 file2 ... fileN
```
The output of your function could look like:
```bash
The oldest file is:
... some-file-name ...
```
**Hint #1**: It suffices to use command chain operators ```&&``` and ```||``` in combination with the test construct ```[[ ... ]]```.  




**Challenge #2**:  As a starting point to this homework, dump the following lines (including the empty lines!) in the file named ```scrambled.txt```:

```bash
Bash  is       an  sh-compatible command language 

       interpreter that     executes commands read 
        
from       the standard   input or from a         file.
Bash also   incorporates     useful features from 


    the Korn and      C shells (ksh and csh).
```

Develop a **Bash** function named ```FileInspector``` which does the following:

1. it takes exactly one argument, which must be a non-empty file
2. parses line-by-line through the file content, in each line counts the number of words, and replaces all occurrences of multiple empty characters with the single empty character
3. removes all empty lines
4. counts the number of non-empty lines
5. finally, the function dumps the reformatted file content in the new file named ```sorted.txt```
6. the last line contains the number of lines

For instance, for the above starting file ```scrambled.txt```, the reformatted new file ```sorted.txt``` shall look like this:

```bash
6: Bash is an sh-compatible command language
5: interpreter that executes commands read
8: from the standard input or from a file.
6: Bash also incorporates useful features from
8: the Korn and C shells (ksh and csh).
Number of lines is 5.
```

The first number in each line is the number of words on that line.

**Hint #1:** To parse line-by-line programmatically through the content of small ASCII files, use **while+read** construct.

**Hint #2:** To parse through all words in a single line, use schematically:

```bash
for Word in ${Line}; do
 ... 
done
```

where **Line** is the name of the variable used in the previous **while+read** step.

**Hint #3:** Use the same **while+read** loop to process a line from the starting file, and to dump in the very same loop iteration the reformatted line to the new file, something like:

```bash
while read Line; do
 ... some code ...
done < scrambled.txt 1>sorted.txt 
```

Operator ```1>``` redirects only the successful output of command into some file.

**Hint #4**: The command **echo** preserves the multiple empty characters only if the arguments supplied to it are embedded within the quotes. Without quotes, 1 or more empty characters become 1 empty character in the **echo** printout.



**Challenge #3:** Write a **Bash** script which, after being sourced, does the following:  

1. Prompts in the terminal with the message: ```Do you want to continue [Y/n]?```
2. If the user has replied 'Y', it prints ```OK, continuing!```, and executes further code in the script
3. If the user has replied 'n', it prints ```Hasta la vista!```, and terminates with the exit status 0
4. For any other reply, it prints: ```Sorry, that option is not supported (yet).```, and terminates with the error exit status 1