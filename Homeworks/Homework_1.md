# Homework #1: Using ```Bash``` aliases as your simplest commands

**Last update: 20200427**

**Challenge #1**: Develop a **Bash** script named ```timeZones.sh``` which is used as 

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

**Hint:** In order to force command **date** to print the current time but corresponding to different time zone, use the following example syntax:

```bash
TZ=Asia/Tokyo date
```

This prints the current time in Tokyo, and switches back immediately to your default time zone. In general, if you define variable and execute the command in the same line like in this example (and without ```;``` separating them!), the value of variable is visible only within the execution of that particular command:

```bash
Var=value command # value of 'Var' is seen only in 'command' during its execution
```

In this case, the command **date** has an internal variable called ```TZ``` , which then we can set temporarily like in this example --- see the _man pages_ of **date**. 

The following would not work in this particular example:

```bash
TZZ=Asia/Tokyo date # wrong, since 'date' doesn't know what to do with variable TZZ
TZ=Asia/Tokyo; date # now variable 'TZ' is set and interpreted in your local
                    # environment and not within the execution of 'date'  
```

The supported time zones are typically sorted out in the directory ```/usr/share/zoneinfo/```, just inspect its content with **ls** command, and figure out the analogous syntax for the time zone you need!

**Challenge #2**: How you would define an alias **tz**, which upon execution produces the same output as the above script? Remark: A user wants to execute **tz** from any directory, not necessarily from the directory in which the script ```timeZones.sh``` sits. To get the absolute path of a directory in **Linux** filesystem hierarchy, execute the command **pwd** ('print working directory') in that directory.  

**Challenge #3**: What do you need to do to make alias **tz** available each time you login on computer?

**Challenge #4**: What do you need to do to make alias **tz** available each time you open a new terminal?

In this homework, you have practiced how to create your own simplest possible command on **Linux** by using **Bash** aliases, and how to make them permanently available in your shell environment.