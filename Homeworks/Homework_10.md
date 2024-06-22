![](../Common_Figures/LinuxBashROOT_logos.png)

# Homework #10: Shell's real-life examples and plotting and histogramming with ROOT

**Last update:** 20240622

**Challenge #1:** Write down a one-line code snippet in **Bash** which will return 0 if the first 20 lines (let's say) in two files are the same, and return 1 otherwise. 

**Hint:** Combine **diff**, process substitution operator ```<( ... )```, and **head** (or **sed**). 

**Challenge #2:** Write down a one-line code snippet that will print on the screen all files in your home directory that were modified within the last 24h.

**Challenge #3:** Develop a **Bash** function named **Converter** which takes as an argument directory. Then, in that directory and all of its subdirectories, it converts all '.eps' files into '.pdf' files. 

**Hint:** To convert a single '.eps' file into '.pdf' file, use the command: **epstopdf someFile.eps** (or alternatively **epspdf** or **ps2pdf** command). 

**Challenge #4:** Develop a **Bash** function named **CleanUp** which takes a directory as a mandatory argument. The second argument, **N**, is a positive integer, and it is optional &mdash; if it is not specified, it defaults to **N = 10**. The script then searches for all files of the specified directory (also recursively in all its subdirectories), and prints **N** largest files, sorted in such a way that the largest file is printed first. The script then deletes these **N** largest files, but the user is prompted to confirm by pressing 'Y' for each file that needs to be deleted. If the user types whatever else, the file is not deleted. 

**Challenge #5:** Implement a piece of code in the vanilla **ROOT** macro (just open the blank file, its content surround within ```{ ... }```, and save it as ```myFile.C```) which will treat the function $$f(x) = ax e^{-bx}$$ as a probability density function (p.d.f.) on the interval $$0 \leq x < 1$$ where $$x$$ is variable and $$a$$ and $$b$$ are parameters (see examples in http://root.cern.ch/root/html/TF1.html).

Set parameters $$a$$ and $$b$$ to be 0.1 and 0.4, respectively, and sample 50000 times variable $$x$$ from the implemented p.d.f. $$f(x)$$. Define a one-dimensional histogram with the floating-point precision TH1F (https://root.cern.ch/doc/master/classTH1F.html) and fill it with all sampled values of $$x$$. 

1. What is the mean value of $$x$$?   
2. What is the 3rd moment of distribution (so-called skewness)?  

**Hint:** Do not calculate these quantities 'manually' &mdash; instead, use the dedicated member functions of the histogram class, and just print them on the screen at the end of your **ROOT** macro.