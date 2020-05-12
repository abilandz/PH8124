# Homework #4: Filtering and reformatting the file content programmatically

Last update: 20190524

**Challenge #1**: As a starting point, please dump the following lines into the file named 'oldData.log':
```bash
22 35 38 97
67 14 83 54
80 18 25 19
79 65 11 97
22  a 44 de
67 19 17 25
41 22 15 68
70 86 28 65
   23 23
57 29 62 89
11 44
69 94 42 22
77 88 61 42
```
Write a script which will:
1. Take the file 'oldData.log' as an argument; 
2. Filter out programmatically from that file into the new file named 'newData.log' only the 2nd and 4th columns, but only from the lines which have the expected valid entries (in this case, expected valid entry is defined as a line which have exactly 4 integers, other lines are discarded);
3. Into the new file, dump also programmatically the sum of entries in the 2nd  and 4th column, i.e. your final format shall be:
```bash
35 97 => Sum is 132
14 54 => Sum is 68
...
88 42 => Sum is 130
```
In the case the data of your experiment or Monte Carlo simulations was dumped in some ASCII files, this challenge is the trivial prototype of operation you typically need to perform: Cleaning up the data sample and doing some reformatting programmatically. 

**Hint #1**: Use **while+read** construct to parse the file content programmatically and ```1>>``` operator to dump the filtered and reformatted content into the new file.

** Hint #2**: To chek if some variable ```Var``` is an integer, please use the following test construct:
```bash
[[ "$Var" =~ ^[+-]?[[:digit:]]+$ ]] && echo "Integer" || echo "Non-integer"
```