![](../Common_Figures/LinuxBashROOT_logos.png)

# Homework #9: Running in parallel and lock mechanism

**Last update:** 20230707

**Challenge #1**: We start this challenge by creating a simple toy dataset, by executing in the terminal the following code snippet:

```bash
for i in {1..100000}; do echo $i; done > toyData.dat
```

The toy dataset consists of first 100000 integers, and in this challenge, the code needs to be developed which will sum them up, but working in parallel, by splitting the initial dataset across multiple processes running in parallel. This challenge illustrates in the very simplified way the frequently encountered problem in high-energy physics, when one needs to split and analyze the large initial dataset with parallel processes, gaining a lot in performance.   

Develop a **Bash** function named **ParallelWorlds**, which does the following:  

   1. It takes one argument: the file holding the initial large dataset (e.g. ```toyData.dat```)   
   2. It checks programmatically for the number of available CPUs on the computer and stores that info in the local variable **NumberOfCPUs**  
   3. Splits programmatically the starting dataset in **NumberOfCPUs** chunks, roughly every chunk shall be of the same size   
   4. Sends the execution for each chunk in the background in parallel, by using subshells. Each subshell sums up the integers in its chunk and stores the final sum for its chunk in some temporary file   
   5. The main code waits for all subshells running in the background to finish, and then just trivially takes the final results for each chunk from temporary files in 4.) and sums them up to get the final result for the whole ```toyData.dat```   
   6. The final printout is: ```The sum is ...``` 

**Hint #1**: To get programmatically number of CPUs, there is a command **nproc**

**Hint #2**: To split the dataset in chunks, the code-snippet like this can be used: 

```bash
sed -n 1,10p toyData.dat > chunk1.dat
```

This will dump only lines 1-10 (both ends included) from ```toyData.dat``` into ```chunk1.dat```, and so on. Both lower and upper end can be supplied to **sed** programmatically via some **Bash** variables. Alternatively, and when chunks are meant to be of equal size, one can automate the splitting procedure with **split** command. This is particularly handy if the content of initial file which needs to be fragmented is line-oriented, like in this example. In that case, this example code snippet can be used:

```bash
split -d -l 10000 toyData.dat someNamingPattern_
```
If the file 'toyData' has 100000 lines, **split** will fragment it into 10 chunks each of which is holding 10000 lines (flag '-l' specifies number of lines in each chunk), and each chunk is named successively ```someNamingPattern_00```, ```someNamingPattern_01```, ..., ```someNamingPattern_09``` (numerical suffices are used, because flag '-d' is specified). In case there is no exact divisor, reminder goes into the last chunk.



**Off the record (not a challenge!)**: Lock mechanism. When processes running in parallel are accessing and modifying the same file, it is extremely important to ensure that that file can be modified only by one process at the time. This is typically achieved by a lock mechanism, and here it is demonstrated how a simple lock mechanism can be implemented in **Bash**. The starting idea is to use some atomic command, i.e. command which is ensured by underlying operating system to be possible only once. One such simple atomic command is

```bash
 mkdir someDir
```

because operating system will allow only one command to make a new directory named 'someDir'. 

Then, functions **Lock** and **Unlock** could be simple-mindedly implemented as follows:

```bash
function Lock
{
 local File=$1
 local Lock="$(dirname $File)/$(basename $File).lock"
 mkdir $Lock &>/dev/null && return 0 || return 1
}

function Unlock
{
 local File=$1
 local Lock="$(dirname $File)/$(basename $File).lock"
 rm -rf $Lock && return 0 || return 1
}
```

Given these two functions, schematic usage of lock mechanism is as follows, when multiple parallel processes are accessing and modifying the same file named 'someFile':

```bash
 # 1. Request the lock for file 'someFile' in the current process:
 while ! Lock someFile; do
  echo " .... Waiting for the lock .... $(date) "
  sleep 1s # this is not necessary, but it reduces a bit stress on the system
 done

 # 2. Lock was obtained for file 'someFile', modify safely that file:
 ... some lines which modify the content of file 'someFile' ...
 
 # 3. Release the lock when done:
 Unlock someFile
```



