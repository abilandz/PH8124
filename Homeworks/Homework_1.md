# Homework #1: Using ```Bash``` aliases as your simplest commands

**Last update: 20200426**

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

This prints the current time in Tokyo, and switches back immediately to the default time zone. In general, if you define variable and execute the command in the same line like in this example, the value of variable is set only for the execution of that particular command. The supported time zones are typically sorted out in the directory ```/usr/share/zoneinfo/```, just inspect its content with **ls** command, and figure out the analogous syntax for the time zone you need!

**Challenge #2**: How you would define an alias **tz**, which upon execution produces the same output as the above script? Remark: A user wants to execute **tz** from any directory, not necessarily from the directory in which the script ```timeZones.sh``` sits. To get the absolute path of a directory in filesystem hierarchy, execute the command **pwd** ('print working directory') in that directory.  

**Challenge #3**: What do you need to do to make alias **tz** available each time you login on your computer?

**Challenge #4**: What do you need to do to make alias **tz** available each time you open a new terminal?

In this homework, you have tested how to create your own simplest possible commands by using **Bash** aliases, and how to make them permanent in your shell environment.

Please send the solution via email to ```ante.bilandzic@tum.de``` , with the subject line which contains the key words ```Homework #1 ``` .