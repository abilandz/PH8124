# Homework #8: Real-life examples

Last update: 20190712

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