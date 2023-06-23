![](../Common_Figures/LinuxBashROOT_logos.png)

# Homework #6: Playing with **Bash** strings and arrays.

**Last update:** 20230622

**Challenge #1:** Write down a **Bash** code snippet which will capitalize only the very last character of a word. Example use case:

```bash
$ Var=abdDef
... desired code snippet here ...
$ echo $Var
abdDeF
```



**Challenge #2**: Write your own version of **date** command, named **Date**, which has the following example printout:

```bash
Today is Wed, JUN 3, 2020
It is precisely 19:12:05
Have a nice time! 
```

Everything is the same as in the standard **date** command, except that:  

1. The output stream is formatted in a different way  
2. The name of the month is printed in all capitals
3. Time zone is dropped 

**Hint**: Implement **Date** as a **Bash** function, in its body execute the standard **date** command, and store the output in an array, something like:

```bash
myArray=( $(date -R) )
```

The flag '-R' produces a more uniform output (number and ordering of fields) across different implementations of **date** command on different operating systems.

Then manipulate all array elements separately, and in combination with **echo** achieve the desired reformatting. 



