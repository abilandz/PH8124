![](bash_logo.png)

# **Bash** functions

**Last update:** 20230526

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
... some file name ...
```
**Hint #1**: It suffices to use command chain operators ```&&``` and ```||``` in combination with the test construct ```[[ ... ]]```.  

**Hint #2**: To loop over all arguments supplied to the function, use the following code snippet:

```bash
for File in "$@"; do
 ... some action on ${File} ...
done
```

 