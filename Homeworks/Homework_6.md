![](bash_logo.png)

# Homework #6: Coding adventures with grep, sed and awk

**Last update:** 20200605

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

Copy and save the above printout in the file ```output.dat```, as the starting point for this exercise. By combining pipes, **grep**, **awk** and **sed**, write down a one-line code snippet which will filter out, reformat and update in-place the file ```output.dat``` with the following new format and content:

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

Make sure that the starting file was stored in some backup file, in the case reformatting fails.

**Challenge #2**: Define your own version of **ls** command named **Ls**, which takes as arguments only directories, and whose printout is:

1. directory name
2. list of subdirectories 
3. files in that directory sorted with respect to size, largest file on the bottom. For each file, the following metadata is printed: ```name month date hour:min size```
4. ignores '.' and '..' in the printout, as well as all hidden files whose name starts with ''.' 

For instance, the output could look like:

```bash
Directory "Lecture"
Subdirectories: Backup Test Leftovers 
test.html                 Jun  02  14:44  23
bash_logo.png             Mar  11  10:22  444
Lecture_7_20200606_0b.md  Jun  06  15:25  1234
```

**Hint #1:** Develop a function **Ls**, in its body execute the standard **ls** with carefully chosen options (check for instance **man ls** for the meaning of the flags '-l', '-S', '-r')

**Hint #2:** To differentiate between files and subdirectories, pipe the **ls** executed with flag '-l' to **grep**, and then just use either **grep -v "^d"** or **grep "^d"** (file metadata begin with 'd' only for directories)   

**Hint #3:** To extract and order the relevant fields, pipe further to **awk** (for files), or store trmporarily in some array (for subdirectories) 

**Hint #4:** To ensure that all columns have the same width, pipe further to **column -t** 







