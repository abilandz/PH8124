# Homework #3: **Bash** functions

Last update: 20200512

**Challenge #1**: Develop the **Bash** function named ```OldestFile``` which will take as an arguments a list of files, and: 

	1. terminate with the error exit status 1 if the user called the function without supplying any arguments
 	2. terminate with the error exit status 2 if some file which the use have supplied does not exist (and print the name of that problematic file)
 	3. print the name of the file which wasn't edited (modified) for the longest period
 	4. then prompt the question to the user: "Do you want to delete that file [y/n]? "
 	5. if the user replied 'y', the function deletes that file, and terminates with the success exit status 0
 	6. if the user replied 'n' just terminate the function with the success exit status 0
 	7. if the user replied something else, print the warning, and terminate the function call with the error exit status 3

The user would like to use your function in the following schematic way:

```bash
OldestFile file1.log file2.log ... fileN.log
```
The output of your function could look like:
```bash
The oldest file is:
... some-file-name ...
Do you want to delete that file [y/n]? 
Deleted. # (only if the answer was 'y')
```
**Hint #1**: It suffices to use command chain operators ```&&``` and ```||``` in combination with the test construct ```[[ ... ]]```.  

**Hint #2**: To loop over all arguments supplied to the function, use the following code snippet:

```bash
for File in $*; do
 ... some action on ${File} ...
done
```

**Challenge #2**: What do you need to do to be able to use the function ```OldestFile``` just like any other **Bash** or **Linux** command (i.e. to be able to call it only by its name from any place in the filesystem, each time you login on computer or open a new terminal)?

 