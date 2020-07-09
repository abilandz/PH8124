### HOMEWORK LEFTOVERS



**Challenge #2**: Develop a **Bash** function named **GarbageCollection** which will in your home directory and in ```/tmp``` directory search for and print on the screen:  

    1. all files and directories whose names begin with 'tmp.' and which were not modified for 1 week

This example is relevant in practice if you use a lot commands like **mktemp** and **mktemp -d** in your code to create temporary files and directories with unique names, and if your code crashed for one reason or another before you deleted those temporary files. Then, one script running centrally in the background can do the garbage collection automatically. 

