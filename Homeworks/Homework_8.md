![](bash_logo.png)

# Homework #8: Jobs and processes

**Last update:** 20230707

**Challenge:** Develop a **Bash** function called **Safeguard**, which does the following:  

1. It takes one argument which must be an integer in the interval (0,100). If the argument is not supplied, it defaults to 80   

2. It checks for all processes for which either 'CPU' or 'MEM' consumption is above the threshold set via the first argument

3. It terminates all problematic jobs and prints the warning message for each in the following example format:

   ```bash
   PID 1234 is doing crazy things: CPU is 99%, MEM is 84%
   Terminated now.  
   ```

**Hint**: To parse through all processes owned by you, use something like:

```bash
local ProcessInfo
while read ProcessInfo; do
 ...  
done < <(top -b -n 1 | grep -w $USER)
```

Here the construct ```<( ... )``` is the _process substitution operator_, and it's basically the shortcut syntax for dumping the command output in a temporary file, and then reading from that file. Flag '-n' specifies the number of iterations **top** should execute before terminating.

