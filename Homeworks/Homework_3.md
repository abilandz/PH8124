# Homework #3: **Bash** functions

Last update: 20200513

**Challenge #1**: Develop the **Bash** function named ```OldestFile``` which takes as arguments a list of files. The function does the following: 

	1. terminates with the error exit status 1 if the user called the function without supplying any arguments

 	2. terminates with the error exit status 2 if some file which the user has supplied does not exist (and it prints then the name of that problematic file)
 	3. prints the name of the file which has not been modified for the longest period
 	4. prompts the question to the user: "Do you want to delete that file [y/n]? "
 	5. if the user replied 'y', the function deletes that file, and terminates with the success exit status 0
 	6. if the user replied 'n' the function just terminates with the success exit status 0
 	7. if the user replied something else, the function prints the warning and terminates with the error exit status 3

The user would like to use your function in the following schematic way:

```bash
OldestFile file1 file2 ... fileN
```
The output of your function could look like:
```bash
The oldest file is:
... some-file-name ...
Do you want to delete that file [y/n]? 
Deleted. # (if the answer was 'y')
Wrong input. # (if the answer wasn't either 'y' or 'n')
```
**Hint #1**: It suffices to use command chain operators ```&&``` and ```||``` in combination with the test construct ```[[ ... ]]```.  

**Hint #2**: To loop over all arguments supplied to the function, use the following code snippet:

```bash
for File in $*; do
 ... some action on ${File} ...
done
```

**Challenge #2**: What do you need to do to be able to use the function ```OldestFile``` just like any other **Bash** or **Linux** command (i.e. to be able to call it only by its name from any place in the filesystem, each time you log in on the computer or open a new terminal)?

 