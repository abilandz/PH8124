# Homework #8: Real-life examples

Last update: 20190712





### # Homework #7: Running processes in parallel

**Challenge #1**: As a starting point for this challenge, please generate the Toy Monte Carlo dataset in the file 'toyData.dat' in the following way:

```bash
for i in {1..100000}; do echo $i; done > toyData.dat
```

Basically, your Toy Monte Carlo dataset consists of first 100000 integers, and in this challenge we will try to develop a code which will sum them up, but working in parallel. 

Please develop a **Bash** function called 'ParallelWorlds', which does the following:

1. It takes one argument, which is the file holding some dataset;
2. It checks programmatically for the number of available CPUs on your machine and stores that info in the local variable ```NumberOfCPUs```;
3. Splits programmatically the starting dataset in NumberOfCPUs chunks, roughly each chunk shall be of the same size;
4. Sends to the background the execution for each chunk in parallel, using subshells. Each subshell stores the final result for its chunk in some temporary file;
5. You main code waits for all subshells running in the background to finish, and then just trivially takes final results for each chunk from temporary files in 4.) and sums them up to get the final result for the whole 'toyData.dat'.

By building on top of this Toy Monte Carlo example, you can in practice split and process in parallel the starting large dataset, gaining a lot in performance. 

**Hint 1**: To get programmatically number of CPUs, please use command **nproc** .
**Hint 2**: To split the dataset in chunks, you could use e.g. ```sed -n 1,10p toyData.dat > chunk1.dat``` to dump only lines 1-10 (both ends included) from 'toyData.dat' into 'chunk1.dat', and so on.





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







