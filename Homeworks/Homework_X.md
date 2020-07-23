### HOMEWORK LEFTOVERS



**Challenge #2**: Develop a **Bash** function named **GarbageCollection** which will in your home directory and in ```/tmp``` directory search for and print on the screen:  

    1. all files and directories whose names begin with 'tmp.' and which were not modified for 1 week

This example is relevant in practice if you use a lot commands like **mktemp** and **mktemp -d** in your code to create temporary files and directories with unique names, and if your code crashed for one reason or another before you deleted those temporary files. Then, one script running centrally in the background can do the garbage collection automatically. 



----------



2/ Define the profile histogram TProfile (https://root.cern.ch/doc/master/classTProfile.html) with two bins of variable size, $${\rm bin}\ 1: 0 \leq x <  0.25$$ $${\rm bin}\ 2: 0.25 \leq x < 1$$

and fill it with all sampled values of 'x'. What is the mean value of 'x' in each bin, and the corresponding statistical errors?

**Remark:** When doing sampling (or some Monte Carlo simulation in general) in ROOT, you have the full control of whether the random sequence is reproducible or not. If you use the constructor of ```TRandom3``` class with 0 as an argument, then ROOT guarantees that random sequence is unique in time and space (this is especially relevant when doing some large scale simulation in parallel in distributed mode). The following code snippet illustrates the point:
```c
 Int_t seed = 0; // 0 : random sequence is unique in time and space
 //Int_t seed = 44; // anything except 0 : random sequence is always the same and corresponds to this particular choice

 delete gRandom;
 gRandom = new TRandom3(seed);
```

Please send me the resulting ROOT macro via email, and I will provide feedback.

**Challenge #2**: In this challenge you will learn how the results of your data analysis have to be formatted in order to make them publicly available. In high-energy physics we use the Durham website (https://www.hepdata.net/) which hosts all the data points in predefined format from all published papers. In particular, the data points from all publications of experiments at Large Hadron Collider you can fetch easily from this repository.

**Example:** The data points from the following ALICE publication https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.105.252302 you can fetch from the Durham website via the direct link https://www.hepdata.net/record/ins877822). You can of course also use the search engine available on the website to dig out the data points from the publication you are after.

Please fetch from the Durham website the data points for Fig. 3 from above mentioned ALICE publication, and try to reproduce as close as possible this figure in your own vanilla ROOT plotting macro (i.e. try to resemble the marker styles, colors, axes labels, legend, etc.).  Forget about STAR measurements in this figure which are indicated with colorful bands and which are there only for comparison sake. 

Please send me the resulting ROOT macro via email, and I will provide feedback.





# Homework #9: Welcome to ROOT

Last update: 20190719

**Challenge #1**: Please download and install ROOT by following the guidelines at https://root.cern.ch/downloading-root


**Challenge #2**: Please download from the following direct link the ROOT file 'example.root': https://cernbox.cern.ch/index.php/s/neogqdE2guLN7mm . This file contains only one trivial example histogram, which will be used only for testing the functionalities of ROOT GUI.
1/ Start ROOT, and by using TBrowser, open in canvas the histogram stored in file 'example.root';
2/ Once histogram is displayed in ROOT canvas, open the editor (just click on 'View', and then on 'Editor'), and add the title "Energy" to x-axis, and "Counts" to y-axis;
3/ Change in editor the histogram's line color from dark blue to red, and the histogram fill collor from white to light red;
4/ Please save the modifed histogram as .pdf file and send it to me via email (just click 'File' and then 'Save As').

In this exercise you learned how you can put the final touch when it comes to the plotting by using ROOT GUI. In the case you want to dump all your changes in the source code which you might want to re-use later for some additional modification in your plot, you need to export your plot in the 'ROOT macro' (just click 'File', then 'Save As', and then as an extension choose 'ROOT macro'). Once you have the source code of your plot in ROOT macro, let's say in file 'someFile.C', you can re-use any time later it by executing:
```bash
root -l someFile.C
```