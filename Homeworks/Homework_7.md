# Homework #7: Running processes in parallel

Last update: 20190705

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



