# Homework #8: Real-life examples

Last update: 20190712

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



**Challenge #1**: Write down one line code snippet in **Bash** which will return 0 if the first 10 lines (let's say) in two files are the same, and return 1 otherwise. 

**Hint:** Usage of temporary files is not needed, use process substitution operator instead.

Building on this example, in practice you can compare whether only the common headers in otherwise different files are the same.

**Challenge #2**: Develop a **Bash** function named 'GarbageCollection' which will in your home directory and in ```/tmp``` directory search for and print:
1. all files and directories whose names begin with 'tmp.*', which belong to you, and which were not modified or accessed for let's say 1 week;
2. all empty files and directories;
2. all files whose size exceeds a certain large treshold, e.g. 1 GB.

This example is relevant in practice if you use a lot commands like **mktemp** and **mktemp -d** in your code to create temporary files and directories with unique names, and if your code crashed for one reason or another before you deleted those temporary files. Then, one script running centrally in the background can do the garbage collection automatically. 

**Hint:** To specify user name in **find**, use option **-user ${USER}** 

**Challenge #3**: Write down one-line code snippet which will convert in the specified directory and in all of its subdirectories all '.eps' files into '.pdf' files. 

**Hint 1:** To convert a single '.eps' file into '.pdf' file, please use command: **epstopdf someFile.eps**
**Hint 2:** Command **find** can find anything...







**Challenge #1:** Please develop a **Bash** function called 'Safeguard', which does the following:

1. It takes optionally two arguments: user name and number ```MAX```. If arguments are not supplied, default them to the output of ```${USER}``` and 80, respectively
2. It checks for all processes for the specified user which are exceeding either 'CPU' of 'MEM' consuption, when compared to ```MAX```
3. Prints only PID numbers of those processes 

**Hint**: Parse through the output of **top -b** with **grep** and **awk**.

By building on top of this example, in practice you can develop a script which will run in an infinite cycle in the background, once per 15 minutes let's say, and which will kill any process with memory leak, which is threatening to freeze down your machine.

