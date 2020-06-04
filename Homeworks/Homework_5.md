![](bash_logo.png)

# Homework #5: Playing with **Bash** arrays.

**Last update:** 20200603

**Challenge #1**: Write your own version of **date** command, named **Date**, which has the following example printout:

```bash
Today is Wed, JUN 3, 2020
It is precisely 19:12:05
Have a nice time! 
```

Everything is the same as in the standard **date** command, except that:  

1. Output stream is formatted in a different way  
2. The name of the month is printed in all capitals
3. Time zone is dropped 

**Hint**: Implement **Date** as a **Bash** function, in its body execute the standard **date** command, and store the output in an array, something like:

```bash
myArray=( $(date) )
```

Then manipulate all array elements separately, and in combination with **echo** achieve the desired reformatting. 

**Challenge #2**: Monte Carlo generator has produced the following output for the _x_ and _y_ components of particle momenta:

```bash
px 0.123 py 0.333
warning: output is zero px 0 py 0
px 1.233 py 3.134
px -0.113 py 0.193
error: incomplete output py 1.123
px 3.311 py -0.011
error: incomplete output px 2.012
px 0.222 py 3.123
fatal: wrong formatting 2.0sf1fse2.32
px -0.388 py 5.136
warning: output is zero px 0 py 0
px 0.324 py -1.133
px 0.355 py -2.134
```

By combining pipes, **grep**, **awk** and **sed**, write down a one-line code snippet which will filter out and reformat the above output into:

```bash
Px = 0.123 , Py = 0.333
Px = 1.233 , Py = 3.134
Px = -0.113 , Py = 0.193
Px = 3.311 , Py = -0.011
Px = 0.222 , Py = 3.123
Px = -0.388 , Py = 5.136
Px = 0.324 , Py = -1.133
Px = 0.355 , Py = -2.134
```



 



