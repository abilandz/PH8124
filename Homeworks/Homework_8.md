![](bash_logo.png)

# Homework #8: Parallel worlds

**Last update:** 20200701

**Challenge**: We start this challenge by creating a simple toy dataset, by executing in the terminal the following code snippet:

```bash
for i in {1..100000}; do echo $i; done > toyData.dat
```

The toy dataset consists of first 100000 integers, and in this challenge, the code needs to be developed which will sum them up, but working in parallel, by splitting the initial dataset across multiple processes running in parallel. This challenge illustrates in the very simplified way the frequently encountered problem in high-energy physics, when one needs to split and analyze the large initial dataset with parallel processes, gaining a lot in performance.   

Develop a **Bash** function named **ParallelWorlds**, which does the following:  

   1. It takes one argument: the file holding the initial large dataset (e.g. ```toyData.dat```)   
   2. It checks programmatically for the number of available CPUs on the computer and stores that info in the local variable **NumberOfCPUs**  
   3. Splits programmatically the starting dataset in **NumberOfCPUs** chunks, roughly every chunk shall be of the same size   
   4. Sends the execution for each chunk in the background, in parallel by using subshells. Each subshell sums up the integers in its chunk and stores the final sum for its chunk in some temporary file   
   5. The main code waits for all subshells running in the background to finish, and then just trivially takes the final results for each chunk from temporary files in 4.) and sums them up to get the final result for the whole ```toyData.dat```   
   6. The final printout is: ```The sum is ...``` 

**Hint #1**: To get programmatically number of CPUs, there is a command **nproc** 
**Hint #2**: To split the dataset in chunks, the code-snippet like this can be used: 

```bash
sed -n 1,10p toyData.dat > chunk1.dat
```

This will dump only lines 1-10 (both ends included) from ```toyData.dat``` into ```chunk1.dat```, and so on. Both ends can be supplied to **sed** programmatically via some **Bash** variables.





