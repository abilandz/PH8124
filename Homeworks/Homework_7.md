![](../Common_Figures/LinuxBashROOT_logos.png)

# Homework #7: Coding adventures with grep, sed and awk

**Last update:** 20230630

**Challenge #1**: A Monte Carlo generator, clearly still under development, has produced the following shaky output for the _x_ and _y_ components of particle momenta:

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

Copy and save the above printout in the file ```output.dat```, as the starting point for this exercise. By combining pipes, command chains ```&&``` and ```||```, **grep**, **awk** and **sed**, write down a one-line code snippet which will filter out, reformat and update in-place the file ```output.dat``` with the following new format and content (note the change 'Px' and 'Py' instead of 'px' and 'py', respectively):

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

**Hint #1:**  For security reasons, within the same pipe chain ```... | ... | ...``` you cannot read and modify on-the-fly the very same file, but it is possible if you combine command chain operators (```&&``` and ```||```) and pipes in the same line. It's perfectly fine to introduce some intermediate temporary file. 

**Challenge #2**: Define your own version of **ls** command named **Ls**, which takes as arguments one or more directories, and whose printout is:

1. directory name. If no arguments were supplied, default to one argument, which is the current working directory (i.e. the directory from which **Ls** was executed)
2. list of subdirectories in that directory
3. files sorted with respect to size, largest file on the bottom. For each file, the following metadata is printed: ```name month date hour:min size``` 

The output of **Ls** is formatted like in this example:

```bash
Directory "Lecture"
Subdirectories: Backup Test Leftovers 
test.html                 Jun  02  14:44  23
bash_logo.png             Mar  11  10:22  444
Lecture_7_20200606_0b.md  Jun  06  15:25  1234
```

If more than one directory was supplied to **Ls**, the above formatting repeats for each directory, separated with an empty line.

**Hint #1:** Develop a function **Ls**, in its body execute the standard **ls** with carefully chosen options (check for instance **man ls** for the meaning of the flags '-l', '-S', '-r')

**Hint #2:** To differentiate between files and subdirectories, pipe the output of **ls** executed with the flag '-l' to **grep**, and then just use either **grep -v "^d"** or **grep "^d"** (file metadata begin with 'd' only for directories)   

**Hint #3:** To extract and order the relevant fields, pipe further to **awk** (for files), or store temporarily in some array (for subdirectories) 

**Hint #4:** To ensure that all columns have the same width in the final printout, simply pipe at the very end to the command **column -t** 

**Challenge #3:** Injecting a new column. Write down one-line code snippet which can be used in the terminal, and which will transfer ASCII file with the content:

```bash
a1 a2 a3 a4
b3 b1 b3 b4
c1 c3 c2 c4
```
into a new ASCII file with the following content: 
```bash
a1 a2 a3 test a4
b3 b1 b3 test b4
c1 c3 c2 test c4
```
