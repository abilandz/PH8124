# Homework #5: Playing with **Bash** arrays.

Last update: 20190614

**Challenge #1**: Write a **Bash** function called **Filter**, which takes exactly two arguments, where each argument consists of all elements of an array, and filters out their intersecting elements. Only one loop in the body of the function is allowed as two nested loops could lead to severe performance penalty for large arrays. The user would like to use function **Filter** in the following schematic way in his code: 
```linux
Array1=( some-elements )
Array2=( some-elements )
IntersectingElements=( $(Filter "${Array1[*]}" "${Array2[*]}" ) || return 1
echo "Intersecting elements are: ${IntersectingElements[*]}"
```
**Hint**: Treat all elements of one array as string and make use of ```=~``` operator within the test construct [[ ... ]].

**Challenge #2**: Write your own version of **date** command, named **Date**, which has the following example printout:
```bash
Fri January 14 19:43:53 CEST 2019
```
I.e. everything is the same as in the standard **date** command, except that you spell out all month names completely ('January' instead of 'Jan', etc.).

**Hint**: Implement **Date** as a **Bash** function, in its body execute the standard **date** command, and echo the desired pattern matches/replacements after using the curly braces gym.

