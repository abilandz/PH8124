# Homework #6: Using grep, sed and awk

Last update: 20190628

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



 







**Challenge #1**: Please develop a **Bash** function called 'Safeguard', which does the following:

1. It takes optionally two arguments: user name and number ```MAX```. If arguments are not supplied, default them to the output of ```${USER}``` and 80, respectively
2. It checks for all processes for the specified user which are exceeding either 'CPU' of 'MEM' consuption, when compared to ```MAX```
3. Prints only PID numbers of those processes 

**Hint**: Parse through the output of **top -b** with **grep** and **awk**.

By building on top of this example, in practice you can develop a script which will run in an infinite cycle in the background, once per 15 minutes let's say, and which will kill any process with memory leak, which is threatening to freeze down your machine.


**Challenge #2**: As a starting point, please save the following text in the file _example.log_:
```linux
000138107 COPIED DELETED
000139123 NOT_COPIED NOT_DELETED
000140447 COPIED NOT_DELETED
```
1.Programatically, insert in that file as a new 3rd column the field 'NOT_MERGED', i.e. the updated file shall look like:
```linux 
000138107 COPIED NOT_MERGED DELETED
000139123 NOT_COPIED NOT_MERGED NOT_DELETED
000140447 COPIED NOT_MERGED NOT_DELETED
```
2.Programatically, count the number of entries in the file for each flag, e.g. provide the following summary:
```linux
COPIED: 2
NOT_COPIED: 1
MERGED: 0
NOT_MERGED: 3
DELETED: 1
NOT_DELETED: 2
```
**Hint**: Parse and manipulate the file content with **grep** and **sed**. For counting, **wc -l** might be handy. 


**Challenge #3**: Define the one-line alias 'my-ls', which will for the current working directory print all files and subdirectories, in the following format:
```bash
name-of-file month date hour:min
```
E.g. the output could look like:
```linux
Lecture_7_20190625_0b.md Jun 25 15:25
test.html                Jun 25 14:44
```
**Hint #1:** Pipe the output of **ls -al** to **awk**, and print only the fields you want to see, and in the order you want them to appear.
**Hint #2:** To ensure that all columns have the same width, pipe the previous printout to **column -t**  .



