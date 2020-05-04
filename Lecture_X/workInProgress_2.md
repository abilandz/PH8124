### wildcard expansion or globbing

 o the process of matching expressions containing wildcards to filenames
 o works with filenames, but perhaps also in some other context
   oo ? => any single character
   oo * => any string of characters
   oo [set] (if you want - to be part of set, put it first or the last, otherwise it indicates range)
   oo [!set] (! only at the very first place has a special meaning, you can either escape it, or place it at some other place)
  o ranges are not cross-platform independent
o ~ fits also here
o very handy with examples with pathname expansions
o expand only to the files or directories which exists => more general is brace expansion (see below)
o cannot be nested













### readline, bind

o readline
   oo command-line editing interface in Bash
   oo a GNU library which can be used by applications requiring a text-based interface
   oo provides editing and text-manipulating features, to ease text entering and editing
   oo equally importantly: it allows standardization of key strokes
   oo for the default key bindings, see man pages of readline (there shall be a list beneath 'Emacs Standard bindings')
   oo the current key bindings: bind -P
   oo readline provides default editing in two modes: vi and emacs
        => both modes provide subsets of commands available in full editors (see examples above)
   oo How to make your own command sets?
   oo key bindings: can customize Bash, either from the command line, or in a special startup file
   oo it is possible also to set readline variables
   oo readline start up file: .inputrc in home directory (default) and  or set env variable INPUTRC to some other file 
   oo when bash starts, it reads .inputrc
   oo .inputrc file: sequence of lines that bind a keyname to a macro or readline function name
         => comments are surprise, surprise, preceeded by #
   oo keyname: either English name, or key escape sequence
   => Example: To bind CTRL-T to the movement commandfor moving to the end of the current line, place the following in .inputrc
        Control-t: end-of-line
  or using key escape sequence
        \C-t: end-of-line
  The \C- is the escape sequence prefix for Control
  oo readline functions: more than 60 are available, see 'man readline'
      end-of-line
      TBC
  oo binding a macro to key sequence. A macro is simply a sequence of keystrokes inside single or double quotes. 
  => Example:  To bind CTRL-T to some text, place this in .inputrc 
       \C-t: "Hi there!"
        Remark: If you want also to bind single or double quotes, just escape them in .inputrc
  oo to update or reload or source the changes in .inputrc in the current terminal, do:
       bind -f  ~/.inputrc
       \M- => Meta (escape) prefix => do not know yet what is this
       \e => escape character => do not know yet how to use it
       \\ => self-evident
       \" => self-evident 
       \'  => self-evident
  oo readline also allows for simple conditionals in .inputrc: $if , $else and $endif
        => the conditional to $if can be: a) an editing mode (use the form mode=); b) a terminal type (use the form term=); c) an application-specific condition
        Example: 
        $if mode=emacs
        \C-t: "Hallo!"
        $endif
        => b) this way for instance we can have terminal specific key binding.  
        => c) this way for instance we can have application specific (if that applications uses readline, of course) key binding. For bash-specific key bindings, use $if bash in the conditinal. This is really WONDERFUL
  oo readline variables 
        => can be set within  .inputrc
     TBI Table 2-19
        => use key word set to set them: 
              Example: Please in .inputrc the following line to enable vi editinf mode:
              set editing-mode vi 

  oo bind
        oo use this command to enforce key binding on the spot, or to update the bindings for the newly updated .inputrc file
        Example 1: bind '\C-t: "Hello 44!"' # yes, '' quotes are essential, as bind apparenly only accepts/expects one argument
        Example 2: bind -f .inputrc
        bind -P : the current key bindings
        bind -p : the current key bindings sent to stdout, redirect them to file, edit, and then use as a new .inputrc file # WONDERFULL
                        Example: "\C-x\C-v": display-shell-version
        bind -l : only readline functions
        bind -u : unbind a function
        bind -r : unbind a key sequence
        bind -x : bind a shell command to key sequence. Example:  bind -x '"\C-l": "ls"' # WONDERFULL
        bind -p : bind a shell command to key sequence. Example:  bind -x '"\C-l": "ls"' # WONDERFULL
       oo bind is a builtin command of the Bash shell. You can use it to change how bash responds to keys, and combinations of keys, being pressed on the keyboard.
            => help bind
       oo bind '"\e[18~":"Hi!"' # press F7, and you will get "Hi!"

From web: https://www.computerhope.com/unix/bash/bind.htm
If you're not sure what the code is for a particular key combination, you can press Ctrl+v at a bash prompt, then press the key combo. This action is called quoted-insert, and it will display the code for the key you pressed. For instance, if you press Ctrl+v, then F7, you will see:
^[[18~
Here, ^[ is an escape character, so to represent this keycode in a string we can use:
"\e[18~"

o see also <https://unix.stackexchange.com/questions/424471/whats-the-difference-between-bashrc-and-inputrc>









### Special files

o The following 3 have special meaning to Bash, and must be in your home directory:
1/ .bash_profile
     oo the most important bash file, executed each time you log in to the system
     oo executed only by the login shell
     oo .profile and .bash_login are synonyms for .bash_profile, read only if .bash_profile is not available
           => the point here is that .profile are read by sh and ksh. So those settings can be retained by bash
           => .login is read by csh , but do not use that!! Difference in syntax is too big
     oo if only .profile and .bash_login are available, .bash_login is read 
     Priority:
     .bash_profile => .bash_login => .bashrc => .profile
     TBI: Why in my case .bashrc is read before .profile ???
2/ .bash_logout
     oo Executed every time when login shell exits
     oo Use case: a) Delete all temporary files; b) Calculate how much time I've been logged in, etc.
3/ .bashrc
     oo Read when we start a subshell (typing bash)
     oo If you need these commands also at log in, just source .bashrc in .bash_profile
     oo If .bashrc is empty, nothing is executed when you start a subshell 
o These 3 files modify your own environment, when you login, start new shell, or log out.
4/ /etc/profile
o If they do NOT exist, your account is using only a system-wide default file /etc/profile
   => sudo is needed to modify it
5/ /etc/bash.bashrc TBI

oo The Environment File
o Although env. variables will always be known to subprocesses, the shell must be explicitly told which other variables, options, aliases, etc. are to be communicated to subprocesses. The solution: put all such definitions in the enviroonment file. bash's default environment file is .bashrc
   => 'rc' suffix is common in Unix to initialization files. According to the folkore, is stands for 'run commands'  
o General rule: as few definitions as possible in .bash_profile, and as many as possible in .bashrc
o The only thing that really needs to be in .bash_profile are env. variables and their exports and commands that aren't definitions but actually run or produce output when you log in
    => Example: You can put date command in .bash_profile
    => Example: Put source .bashrc in .bash_profile
o aliases and options shall go in .bashrc













### Functions

o To unset function definiton: unset -f <funcname>

o To find out which functions are defined in the current session: declare -f
    => The function names and implementations are printed in alphabetical order
    => To see only the names without implementation: declare -F 
    => You can see function implementation also with: type <funcname>
o SO: How to find the file where a bash function is defined? => See the thread: https://unix.stackexchange.com/questions/322817/how-to-find-the-file-where-a-bash-function-is-defined
    bash --debugger
    declare -F AAAARGH
    AAAARGH 5 /home/abilandz/development/scripts/1b/functions_1b.sh # WONDERFULL









#### mktemp

How to make programatically temporary file or directory? 

- Temporary file: **mktemp**
- Temporary directory: **mktemp -d**













#### diff

**Example:** How to check if two files are the same?  Use: 

```bash
diff <file1-name> <file2-name>
```

If two files are the same, **diff** returns exit status 0, if files are different, the exist status is non-zero, and you get the output summarizing the difference between two files.

**Example:** How to check if the output of two commands is exactly the same? Here is precisely where the process substitution operator ```<( ... )``` becomes very handy! Use: 

```bash
diff <(first-command) <(second-command)
```

For instance, you can check if only first 10 lines in the file are the same with:

```bash
diff <(head -10 <file1-name>) <(head -10 <file2-name>)
```













#### **pdfjam**
11) How to merged together pdf files in one large single file?
    o pdfjam <file1>.pdf <file2>.pdf ... <fileN>.pdf -o <merged-file-name>.pdf









#### **file**
12) File doesn't have an extension? How to get programatically the file type?
    o file <file-name>







#### **Random numbers**
    o echo $RANDOM    
    o shuf -i 1-10
    o shuf -i 1-10 -n 1





**Example: sourced script vs. executed script**



**Example:** Consider the following simple implementation and save it in the file ```someScript.sh``` :

```bash
#!/bin/bash
echo $Var
echo "Process ID of script: $$"
Var=20
return 0
```

On the other hand, the following, slightly modified implementation, save in the file ```someScript``` :

```bash
#!/bin/bash
echo $Var
echo "Process ID of script: $$"
Var=20
exit 0
```

These two implementations are identical, with the only difference being the exit status specification. Now in your terminal execute first:

```bash
Var=10
echo "Process ID: $$ 
echo "Before: $Var"
source someScript.sh
echo "After: $Var"
```

The output is:

```bash
Process ID: 6164
Before: 10
10
Process ID of script: 6164
After: 20
```

Now we do the same for executed script. TBI



TBI : Introduce special variable $$







**Difference between functions and scripts (extra)**    

Another interesting difference between scripts and functions is the handling of the zeroth (```${0}```) positional parameters. All other positional parameters (```${1}, ${2}, ...```) and corresponding special variables (```${*}, ${@} and ${#}```) have exactly the same meaning within script and function body.  For the scripts, ```${0}``` holds the name of the command used to invoke the script, but only if the script is executed as an executable. If the script is not an executable, i.e. when script is sourced, then only typically 'bash' is printed as the content of ```$0```, because then essentially ```bash some-script.sh``` is being executed behind the scene. 

When it comes to the functions, ```$0``` in the function body retrieves the same meaning as for the scripts --- it is not set to the function name, but rather to the command used to initialize function definition from some file. 

