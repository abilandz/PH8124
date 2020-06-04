![](bash_logo.png)

# Homework #5: Playing with **Bash** arrays.

**Last update:** 20200604

**Challenge #1**: Write your own version of **date** command, named **Date**, which has the following example printout:

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
myArray=( $(date) )
```

Then manipulate all array elements separately, and in combination with **echo** achieve the desired reformatting. 



