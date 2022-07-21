![](bash_logo.png)

# Homework #9: Real-life examples

**Last update:** 20220721

**Challenge #1:** Write down one-line code snippet in **Bash** which will return 0 if the first 20 lines (let's say) in two files are the same, and return 1 otherwise. 

**Hint:** Combine **diff**, process substitution operator ```<( ... )```, and **head** (or **sed**). 

**Challenge #2:** Write down one-line code snippet which will print on the screen all files in your home directory which were modified within the last 24h.

**Challenge #3:** Develop a **Bash** function named **Converter** which takes as an argument directory. Then, in that directory and all of its subdirectories it converts all '.eps' files into '.pdf' files. 

**Hint:** To convert a single '.eps' file into '.pdf' file, use the command: **epstopdf someFile.eps** (or alternatively **epspdf** or **ps2pdf** command). 

**Challenge #4:** Develop a **Bash** function named **CleanUp** which takes as a mandatory argument a directory. The second argument **N** is a positive integer, and it is optional - if it is not specified, it is defaulted to **N = 10**. The script then searches for all files of the specified directory (also recursively in all its subdirectories), and prints **N** largest files, sorted in such a way that largest file is printed first. The script then deletes these **N** largest files, but the user is prompted to confirm by pressing 'Y' for each file which needs to be deleted. If user types whatever else, the file is not deleted. 
