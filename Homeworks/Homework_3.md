# Homework #3: Bash functions

Last update: 20190518

**Challenge #1**: Develop the **Bash** function named ```LargestFile``` which will take as an arguments bunch of files, and print only the one with the large
st size, together with its size. The user would like for instance to use your function in the following way:
```bash
LargestFile file1.log file2.log ... fileN.log
```
The output of your function could look like:
```bash
The file with the largest size is:
... some-file-name ...

Its size is:
... some-size ...
```
After looking at this output, user then can decide whether or not to delete this file. If some file that user passed does not exist, bail out with error stat
us 1 and error message pointing out which file doesn't exist.

**Two hints**: To loop over all files passed as arguments to the function, you could use the following:
```bash
for File in $*; do
 echo "file is: $File"
done
```

To store programmatically output of a command execution directly into variable, you could use ```$( ... )``` construct, for instance:
```bash
FileSize=$(stat -c %s some-file.log)
echo $FileSize # prints size of some-file.log
```

**Challenge #2**: Develop the **Bash** function named ```EmptyDirectory``` which will take as arguments exactly one argument, which must be a directory. The
function returns 0 if directory is empty, 1 if directory is not empty, and 2 if user passed as an argument something else instead of directory (also non-exis
ting directory for simplicity let's classify this way). User wants to use your function in the following example way:
```bash
EmptyDirectory some-directory && echo "Empty" || echo "Not empty or non-existing"
```

**Hint**: Check the documentation of **ls** command, and then just emulate in your function usage of that command with one specific flag.

**Please answer the following questions:**
1. Please send via email the source code of both functions above --- I will try to use them on my computer and will provide a detailed feedback as a genuine
user!
2. What do you need to do in order to be able to use functions ```LargestFile``` and ```EmptyDirectory```  straight as any other **Linux** command in ay new
terminal you open?