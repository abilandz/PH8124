![](bash_logo.png)

# Homework #5: Mastering the command substitution operator.

**Last update:** 20220624

The developer is testing the time duration of a particular code segment in Bash script to execute. He would like to have the function **TimeDuration**, which he would like to use in the following generic way:

```bash
Started=$(date -R)
... some code ... 
Finished=$(date -R)
TimeDuration "$Started" "$Finished"
```

The desired example printout is formatted as:

```bash
Time duration is: 12345 seconds.
```

**Challenge**: Develop the function **TimeDuration** which will accomplish the above goal. 

**Hint**: In the body of **TimeDuration** convert the two input time stamps, stored into variables 'Started' and 'Finished', into the new format known as 'Unix epoch'. 'Unix epoch' is the number of seconds that have elapsed since January 1, 1970, for instance:

```bash
$ CurrentTime=$(date -R)
$ echo $CurrentTime
Fri, 24 Jun 2022 11:17:51 +0200
$ UnixEpochTime=$(date -d "$CurrentTime" +%s)
$ echo $UnixEpochTime
1656062271
```

After that, simply use ```$(( ... ))``` to perform integer subtraction. 

**Remark**: Yes, the developer could have obtained directly the relevant time stamps in the 'Unix epoch' format with

```bash
# print time in seconds since Unix epoch:
$ date +%s
1656062271
```

But the point behind this exercise is to demonstrate that **date** can internally interpret time stamps written in so many different formats, and convert them automatically into common 'Unix epoch'. For instance:

```bash
$ date -d "Fri, 24 Jun 2022 11:17:51 +0200" +%s
1656062271
$ date -d "24 Jun 2022 11:17:51 +0200" +%s
1656062271
$ date -d "2022-06-24 11:17:51" +%s
1656062271
```

The last example above is the time stamp format used by ALICE Collaboration at Large Hadron Collider, in the log files of jobs running on Grid.