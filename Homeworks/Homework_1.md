# Homework 1: Using shell aliases as your simplest commands

**Last update:** 20260429-4

![](<../Common_Figures/LinuxBashROOT_logos (1).png>)

**Challenge #1**: Develop a **Bash** script named `timeZones.sh` which is used as

```bash
source timeZones.sh
```

and produces the following example output:

```bash
The current time in New York is:
Sun Apr 26 12:46:27 EDT 2020

The current time in Tokyo is:
Mon Apr 27 01:46:27 JST 2020
```

**Hint:** In order to force the command **date** to print the current time but corresponding to a different time zone, use the following example syntax:

```bash
TZ=Asia/Tokyo date
```

This prints the current time in Tokyo, and switches back immediately to your default time zone. In general, if you define a variable and execute the command in the same line like in this example (and without `;` separating them!), the value of the variable is visible only during the execution of that particular command:

```bash
Var=value command # definition 'Var=value' is available only in 'command' during its execution
```

In this case, the command **date** has an internal variable called `TZ` , which can be set temporarily like in this example — see the _man pages_ of **date** for further details.

The following will not work:

```bash
TZZ=Asia/Tokyo date # wrong, since 'date' doesn't know what to do with variable TZZ  
```

Also, this is wrong in this context:

```bash
TZ=Asia/Tokyo; date # now variable 'TZ' is set and interpreted in your local
                    # environment and not within the execution of 'date',
                    # which runs in its own process
```

At the expense of polluting the local environment, this works:

```bash
export TZ=Asia/Tokyo; date # variable 'TZ' is exported, i.e. set to be global;
                           # it is set to a new value persistently both in the local environment
                           # and in the environment of any new process started from it 
```

Based on the above example, we see the advantage of a special `Var=value command` syntax — values of environment variables can be changed for the execution of the command, and those changes are not propagated in the current working environment when that command terminates.

The supported time zones are typically sorted out in the directory `/usr/share/zoneinfo/`, just inspect its content with the **ls** command, and figure out the analogous syntax for the time zone you need!

**Challenge #2**: How would you define an alias **tz**, which upon execution produces the same output as the above script `timeZones.sh`?

**Remark:** A user wants to execute **tz** from any directory, not necessarily from the directory where the script `timeZones.sh` sits. To get the absolute path of a current directory in **Linux** filesystem hierarchy, use the environment variable **PWD** ('print working directory') or execute the command **pwd** in that directory.

**Challenge #3**: What do you need to do to make alias **tz** available each time you login on a computer?

**Challenge #4**: What do you need to do to make alias **tz** available each time you open a new terminal?
