### String manipulation

**Example:** In order to lower/upper cases of characters, , ,, ^ and ^^ can also cope with the ranges:




```bash
test=aBcDeF
echo ${test,,[A-D]}
abcdeF



**Example:** if var is @, the length is number of PPs starting at offset. AB: Here PP1 is offset 1. WONDERFULL!
Example: How to print only 3rd and 4th PPs?
source test.sh a b c d e
echo ${@:3:2} # start at PP3, and length is 2, i.e. 3rd and 4th
c d



**Example:** How to ignore first two PPs?
echo ${@:3} # start at PP3, take all other PPs until the end. WONDERFULL!


oo Extended Pattern Matching
o Bash provides a further set of pattern matching operators if the shopt option extglob is switched on. AB: In my case, by default it's switched on
o The operator takes one or more string separated with the vertical bar: |
*(<pattern_1>|<pattern_2>|...) => matches zero or more occurences of the given patterns
+(<pattern_1>|<pattern_2>|...) => matches one or more occurences of the given patterns
?(<pattern_1>|<pattern_2>|...) => matches zero or one occurences of the given patterns
@(<pattern_1>|<pattern_2>|...) => matches exactly one of the given patterns
!(<pattern_1>|<pattern_2>|...) => matches anything except one of the given patterns

Example 1:
VAR=ALICE_ATLAS_CMS_LHCb
echo ${VAR//+(AL|CM)/44} # replace one or more occurences, null strings do NOT count
44ICE_ATLAS_44S_LHCb
echo ${VAR//*(AL|CM)/44} # replace one or more occurences, null strings do count
4444I44C44E44_44A44T44L44A44S44_4444S44_44L44H44C44b

TBI AB: practice this further, dok ne sjedne. Have a look again on the examples below Table 4-3 on page 99

=> IMPORTANT #1: The values provided can contain also the shell wild card: 
VAR=1_2_3_4
echo ${VAR//+([0-9])/ABC}
ABC_ABC_ABC_ABC

=> IMPORTANT #2: The patterns can be nested!!!!
Example 1: list all files, except the ones beginning with 'temp followed by number':
ls !(temp+([0-9]) # WONDERFULL
Example 2: list all files, except the ones: 1/ beginning with 'temp followed by number', and 2/ beginning with 'tempa and tempb':
ls !(temp+([0-9])|temp+([ab])) # WONDERFULL


=> Few remarks for the last 2: # and % from above maintain their meaning. If pattern_2 is NULL, pattern_1 is deleted. If 'var' is @ or *, the operation is applied to each positional parameter in turn and the expansion is the resultant list

Example 1:
#!/bin/bash
echo ${@//a/A}
source test_today.sh aaa BBB aaa
AAA BBB AAA


Finally, we introduce the special meaning of pattern-matching operators ```#```, ```##```, ```%``` and ```%%```. They are used when we want to manipulate pattern, but only if it appears at the very beginning or at the very end of the string. Their generic syntax is summarized here:
1.```${Var#pattern}``` : if the pattern matches the beginning of variable's value, delete the shortest part that matches and return the rest
2.```{Var##pattern}``` : if the pattern matches the beginning of variable's value, delete the longest part that matches and return the rest
3.```${Var%pattern}``` : if the pattern matches the end of variable's value, delete the shortest part that matches and return the rest
4.```${Var%%pattern}``` : if the pattern matches the end of variable's value, delete the longest part that matches and return the rest


Example 2:
FILE=/home/abilandz/Tutorials/PANDOC/file.44.log
echo ${FILE##/*/} # file.44.log => basically, remove from begining the longest match of /*/ => no need to escape!
  => the same effect is achieved with echo ${FILE##/*\/ or echo ${FILE##\/*\/
echo ${FILE#/*/} # abilandz/Tutorials/PANDOC/file.44.log
echo ${FILE%.*} # /home/abilandz/Tutorials/PANDOC/file.44
echo ${FILE%%.*} # /home/abilandz/Tutorials/PANDOC/file
echo ${FILE%/*} # /home/abilandz/Tutorials/PANDOC AB: to get parent dir, same as dirname TBI test further
=> Remember two patterns: 
      /*/ => anything between two slashes, including slashes
       */ => anything before the slash, including slash
        .* => anything after dot 
=> # and ##, % and %%, produce the same result, unless they are used with *
 
o basename is equivalent to ${VAR##*/} . But it's slower, as it runs its own separate process!o
o dirname is equivalent to ${VAR%/*} . But it's slower, as it runs its own separate process!
 
Example 3:
echo -e "${PATH//:/'\n'}"
/home/abilandz/bin
/usr/local/sbin
/usr/local/bin
/usr/sbin
/usr/bin
/sbin
/bin
/usr/games
/usr/local/games
```













### printf

oo printf 
o more powerful than echo
o powerful system-level printing library
o unlike echo, by default it doesn't provide newline
=> AB: as in echo, escape characters are only interpreted within " "

o generic usage: printf format-string [arguments]
format-string : string that describes the format specifications (best supplied as a string constant in quotes). If the format requires more argments than supplied, extra formats behave as null strings. Format is preceeded by % and the specifier is one of the characters in Table 7.4 on page 171
[arguments] : list of string or variables that correspond to the format specifications

1/ format specifiers (TBI AB just few examples here, for the whole list, have a look at Table 7.4 on page 171)
%c : ASCII character (prints first character of the corresponding argument)
$ printf "Hello %c. %c.\n" Ante Bilandzic
Hello A. B.
%d = %i : decimal integer
$ printf "%d\n" 44
44
$ printf "%d\n" 44.44
bash: printf: 44.44: invalid number
44

%e : floating point format [-]d.precisione[+-]dd
%E : floating point format [-]d.precisionE[+-]dd
%f : floating point format [-]d.precision

%g : conversion TBI AB have a look again   
%G : conversion TBI AB have a look again   

%s : string
%% : literal %

Example: Specifying the width and alignment of output fields WONDERFULL
$ printf "|%10s|\n" hello
|     hello|
$ printf "|%-10s|\n" hello
|hello     |

generic: width.precision
$ printf "|%10.2s|\n" hello
|        he|
$ printf "|%-10.2s|\n" hello
|he        |

The precision modifer: for string number of characters, for decimal or floating point numbers it's number of digits

=> Both width and precision modifier can be specified dynamically => use *

VALUE=$(bc -l <<< 'scale = 10; sqrt(2)')
printf "|%*.*G|" 20 4 $VALUE

IMPORTANT: If this doesn't work: 
$ printf "%f\n" 4.4
bash: printf: 4.4: invalid number
0,000000

It simply means that "," instead of "." shall be used in the floating point expressions. The relevant env. variable is LC_NUMERIC (to see other local specifications (e.g. time format, just  type locale )) . To change this, just add to .bashrc

export LC_NUMERIC="en_US.UTF-8"

$ printf "%f\n" 4.4
4.400000

Example: Floating points
$ bc -l <<< 'scale = 20; sqrt (2)'
1.41421356237309504880
$ printf "|%20.5f|\n" $(bc -l <<< 'scale = 20; sqrt (2)')
|             1.41421|
$ printf "|%-20.5f|\n" $(bc -l <<< 'scale = 20; sqrt (2)')
|1.41421             |

o Meaning of precision varies with the control letter, see Table 7.5 for detais. E.g. for %s, it denotes number of characters to be printed
=> TBI AB Have a detailed look at Table 7.5

o flags: they preceed the field width and precision
'-' : left-justify the formatted value within the field
space : prefix positive values with a space, and negative with minus

- : always prefix numeric values with a sign, even if the value is positive

# : Use an alternat form => TBI AB re-read and test this

0 : Pad output with zeros, not spaces.  => TBI AB re-read and test this

o bash-specific prinf statements (not portable!)
%b : use instead of %s, to treat \n in the same way as echo
$ printf "%s\n" "test1\ntest2"
test1\ntest2
$ printf "%b\n" "test1\ntest2"
test1
test2

%q : use instead of %s, and print a string in such a way, that it can be used as shell input
$ printf "%s\n" "a b  c    d"
a b  c    d
$ printf "%q\n" "a b  c    d"
a\ b\ \ c\ \ \ \ d























### Command line processing

o lines are separated into words, according to delimiters stored in env. variable IFS
o How shell processes command line input? 
   => Each line that the shell reads from the standard input or a script file is called a pipeline : it contains one or more commands separated by zero or more pipe characters |
   => For each pipeline it reads, the shell breaks it up into commands, sets up the I/O for the pipeline, then does the following for each command (see Fig. 7.1)
1/ Splits the command into tokens that are separated by the fixed set of 9 metacharacters: SPACE    TAB    NEWLINE    ;    ,    <    >    |    &    
2/ Checks the first token of each command to see if it's a keyword  with no quotes or backslashes. If it's an opening keyword, such as if  and control structure openers, function, { or ( , then the command is actually a compund command. The shell sets the things up internally for the compound command, reads the next command and starts the process again. If the keyword is not  a compund command openener (e.g. then, else, do, fi, done, or a logical operator), the shell signals a syntax error.
3/ Checks the first word of each command against the list of aliases. Ifa match is found, it substitues the alias's definition, and goes to Step 1; otherwise it goes at Step 4. This scheme allows recursive aliases. It also allows aliases for keywords to be defined (e.g. you can perfectly well have: alias procedure=function)
4/ Performs brace expansion. E.g. a{b,c} becomes ab ac
5/ Substitues the user's home directory $HOME for tilde if it is at the beginning of a word. Substitues user's home directoryu for ~user* . Remark: The current direectory $PWD is replaced by ~+, and $OLDPWD with ~-
6/ Performs parameter substitution for any expression that starts with a dollar sign $
7/ Does command substitution for any expression of the form $(string)
8/ Evaluate arithmetic expressions of the form $((string))
9/ Takes the parts which results from 6/, 7/ and 8/, and splits them into words again. This time it uses the characters in $IFS as delimiters, instead of 9 metacharacters in Step 1
10/ Performas pathname expansion, a.k.a. wildcard expansion, for any occurences of:    *   ,  ?   ,  and   [/] pairs
11/ Uses the firs word as a command by looking up its source according to the rest of the list: a) as a function command; b) as a built-in; c) as a file in any of the directories in $PATH
12/ Runs the command, after setting I/O redirection and other such things

These 12 steps are not the whole story. There are still 5 ways to modify the process: quoting, using command, builtin or enable, and using the advanced command eval .







### 







### make

=> keeps track of multiple files in a particular project, some of which depend on the others
=> it makes sure that when you change a file, all of the other files that depend on it are processed
=> make figures out which files need to be re-processed, after a certain change has been made
o How does make does this?
=> Simple: It compares the modification times of the input and output files (called sources and targets in make terminology), and if the input file is newer, then make re-processes it
=> You tell make which files to check by building a file called makefile that has constructs like this:
target : source1 source2 ...
    commands to make target
\# This syntax essentially says: For target to be up to date, it must be newer than all of the sources. If it's not, run the commands to bring it up to date
\# The commands are on one or more lines that must start with TABs
\# See examples in ~/Tutorials/LINUX/MAKE
\# AB: It seems make is checking only the timestamp difference between target and source, i.e. touch <source-file> wil trigger the default action, even if the file is the same
o TBI AB : have a look again at nice example on page 189, how to leading order to implement yourself what make is doing. And its continuation as Exercise 2 on page 193
o TBI AB: solve exercises 3) a-g on pages 194 and 196























### command, builtin, enable

o How the alter the default command look up order?
   oo Reminder of the default order: 1/ alias; 2/ function; 3/ built-in; 4; scripts and executables (when ordering in PATH matters)

`#!/bin/bash
alias testMe='echo Alias'
function testMe
{
 echo Function
}

# source the above file

$ testMe
Alias

o This default order can be modified with the three built-ins: command, builtin and enable
1/ command : removes alias (as a side effect, since only the first word is looked up for alias, never the argument, see list of 12 steps) and function look up.
                            Only built-ins and commands found search PATH are takein into account
                            Usage: command <some-command>
                            Options:
                            -p : use a default value for PATH that is guaranteed to find all of the standard utilities. Directories in your PATH are ignored.
                            -v : print a description of COMMAND similar to the `type' builtin (AB: but it works only for commands, not for aliases, functions and built-ins TBI AB test further)
                            -V : same as type

- : turns off firther option checking TBI AB not tested
  Classic example which explains everything:
  function cd
  {

# some fancy stuff

 command cd
}
\# When we call cd, funtion is called, then something fancy happens, after we call buil-in cd. That's all. Without using command cd , i.e. having only cd, the function would call itself recursively!!

2/ builtin : as the name says it, looks up only for built-ins, ignoring aliases, functions and commands found in PATH

3/ enable : as the name says it, enables and disables shell built-ins. Disabling e.g. built-in echo, we can use GNU echo /bin/echo, without specifying the abs path to it.
                      Few selected options:
                      -a : print a list of builtins showing whether or not each is enabled
                      -n : disables a built-in or (without arguments) displays a list of disabled built-ins
                                $ enable -n
                                $ enable -n echo
                                $ enable -n
                                enable -n echo
                      -p : list of all built-ins. The ones which were disabled, are now NOT on the list. They do appear if you use -a
                      => After you have disabled built-in, you re-enable it simply with: enable <that-built-in>
                                $ type echo
                                echo is a shell builtin
                                $ enable -n echo
                                $ type echo
                                echo is /bin/echo
                                $ enable echo
                                $ type echo
                                echo is a shell builtin

Off-the-record:  
1/ read this interesting thread TBI AB : read: https://askubuntu.com/questions/960822/why-is-there-a-bin-echo-and-why-would-i-want-to-use-it
2/ How to create and use your own built-in commands? TBI AB See Appendix C, but apparently enable is used also in that context
3/ TBI AB See footnote on page 186, on how to disable enable ;-)















### CTRL, ALT, ESC

o CTRL
o emacs mode
o CTRL + something. A special character, few of which can be interpreted directly by the operating system as a special commands.
o can differ from system to system, to see the current setting, type: stty -a
o not recognized by the bash itself, but by so called tty driver (which controls input output to from terminal)
   oo A : beginning of line
   oo B : move backward one character (AB: same as left arrow)
   oo C : stops, or tries to stop, the command that is currently running
   oo D : exit the current shell if the command line is empty, or delete the current character (even if its empty character) and move forward
   oo E : end of line
   oo F : move forward one character (AB: same as right arrow)
   oo H : delete the previous character (even if its empty character) (same as DELL)
   oo K : delete forward to the end of line
   oo L : clears the screen
   oo M : same as RETURN
   oo N : next line in history (same as down-arrow, but CTRL version can be used on all keyboards)
   oo O : same as RETURN, then display next line in command history. Yes, this one deals with command history, we can retype the whole command chain this way => see the last paragraph on page 35
   oo P : previous line in history (same as up-arrow, but CTRL version can be used on all keyboards)
   oo R : inverse history search
   oo Q : restart the stream flow on the terminal
   oo S : freeze the stream flow on the terminal 
   oo T : Transpose two characters on either side of point and move point forward by one
   oo U : delete all command line from the beginning to the point
   oo V : quoted insert. Try for instance CTRL-V and then CTRL-D, the special meaning of the letter is stripped off, and what you see is only ^D
   oo W : delete previous word from the current position backward, not including the current position. To do it forward, use ALT+D
   oo X : 
   oo Y : paste the lastly deleted thing - opposite of W (i.e redo W, or paste is somewhere else)
   oo \ : use in the same spirit as C, but only if C doesn't work, as C gives more opportunity to job to clean up before terminating
   oo [ : same as ESC

o ALT
oo D : delete next word from the current position forward, including the current position. AB: same as ESC+D
oo B : move one word backwards

o ESC
o AB: unlike ALT and CTRL, ESC must be pressed again and again
o AB: Operates on words, not on single characters. Command line editing can be split into: a) basic commands (operate on single character); b) word commands (ESC); c) line commands (operate on line, e.g. CTRL-A) 
oo B : move one word backwards same as ALT
oo C : capitalize word after point
oo F : move one word forward
oo U : change word after point to all capital letters
oo L : change word after point to all lowercase letters => for instance, what to do after by accident you pressed CAPS LOCK, and didn't notice 
oo for the rest, see table 2.2 on page 30 
o word : sequence of alphanumeric characters (> is not a word)
oo < : move to the first line of history list
oo > : move to the last line of history list
oo . : Insert last word in previous command line (typically the last argument) after point. Example: Run several commands on the same file
oo _ : same as .



















### Shell options

o Options
oo aliases cannot change the shell overall behavior, shell options can
oo shell options are either on or off
oo some options are arcane ;-)
oo generic use:
 set +o <option-name>
 set -o <option-name>
or
 set +o <option-name_1> -o <option-name_2>
oo Counterintuitive flags:
+o is off
-o is on
oo + is afterthought, as - is a default way to specify an option in UNIX
oo for each option, there is also an abbreviation (leftover from sh, their usage is discouraged) 
     Example: set -o noglob is the same as set -f
oo most common options (27 in total, see Appendix B for the other TBI):
 emacs : enter emacs editing mode (ON by default)
 vi : enter vi editing mode (ON by default)
 ignoreeof : disable logging off with CTRL-D. Same effect as shell variable IGNOREEOF=10
 noclobber : doesn't allow redirection > to overwrite an existing file
 noglob : doesn't expand wildcards in filenames (wildcard expansion is sometimes called globbing)
 nounset : indicates an error when trying to use undefined variable
oo To see the status of options: set -o

oo shopt
=> after 2.0, a new built-in for configuring shell behavior
=> idea: to replace env. variables and set -o gym
shopt -o : backward compatibility, same effect as set -o
o Generic syntax: shopt <options> <option-names>
   From help shopt
      -o        restrict OPTNAMEs to those defined for use with `set -o'
      -p        print each shell option with an indication of its status
      -q        suppress output
      -s        enable (set) each OPTNAME
      -u        disable (unset) each OPTNAME
o Default action is to unset the named option
o shopt without arguments lists all options and their status
o shopt -s without arguments lists all options which are on
o shopt -u without arguments lists all options which are off
o AB: options for shopt and set can differ, e.g. hashall option is available only for the latter



















### Shell variables (extra) 

**Shell Variables (general)**

o part of environment that you want to modify, but cannot be expressed as on/off choice
   => characteristics of this type are stored in shell variables
o bash keeps track of built-in variables
o echo prints arguments only after bash has evaluated them
o if the variable is undefined, echo will print a blankline 
o quoting: some special characters within "" are still interpreted, while within '' not a single special character is interpreted
o $ survives "", meaning variables are evaluated

**Built-in Variables**
o some of them are arcana for hackers
o complete list is in Appendix B
1/ Editing mode variables
oo HISTCMD : the history number of current command
      => if we unset it, the counter is gone 
oo HISTCONTROL : a list of patterns, separated by ":". List of possible patterns:
      ignorespace => lines beginning with space are not entered in history list
      ignoredups => lines matching the last history lines are not entered
      erasedups => all previoius lines matching the current line are erased, before the line is saved  
      ignoreboth => enables both ignorespace and ignoredups (my default setting)
oo HISTIGNORE : a list of patterns, separated by ":". List of possible patterns: TBI read again the explanation from Table 3.4 on page 64
oo HISTFILE : the name of the history file, the default is ~/.bash_history
oo HISTFILESIZE : the size of the history file, the default in my case is 2000. When this variable is set, the history file is truncate
oo HISTSIZE : the maximum number of commands to remember in the history file. The default in my case is 1000
      TBI: see here the explanation between HISTFILESIZE and HISTSIZE: https://stackoverflow.com/questions/19454837/bash-histsize-vs-histfilesize
      TBI: there is also a bit of explanation in the middle of page 65 
oo HISTTIMEFORMAT : WONDERFULL!!!!
      Example: HISTTIMEFORMAT="%y/%m/%d %T" and now all enties in the hisroty file appear with the time stamp, e.g.
1102  19/02/18 08:22:25 help fc
1103  19/02/18 08:23:15 echo $HISTCONTROL
1104  19/02/18 08:27:28 hostory
1105  19/02/18 08:27:31 history
1106  19/02/18 08:29:41 echo $HISTFILE
      => if the history lines didn't have the time stamp, then the new time stamp is the time when this variable was set
      => if HISTTIMEFORMAT was changed, the time stamps in history file are modified according to the new setting
      TBI: read again the explanation from Table 3.4 on page 65
      TBI: read again page 66 about it, also Table 3.5 about the possible time stamps 
oo FCEDIT : path of the editor used by 'fc' command

**3/ Mail variables**
TBI read this eventually

**4/ Prompting variables**
o shell's prompt is not engraved in stone
o it is possible to encode date and current directory in the prompt, etc.
o bash uses four prompt strings:
PS1 : primary prompt string, the default value is: "\s-\v\$ "
PS2 : secondary prompt string, the default value is: ">" . 
         => Used when after incomplete commands, RETURN is pressed. 
         => It indicates that the command must be finished (e.g. when we forget to close the quotes)
PS3 : TBI explained later
PS4 : TBI explained later
=> It seems that quotes are mandatory when setting PS* variables TBI check this AB
o Available prompt customizations:
\a : bell 
\A : the current time (24-hour HH:MM)
\d : date (e.g. format is "Mon Feb 18")
\D : TBI
\e : escape
\H : the hostname
\h : the hostname up to the first "."
\ j : the number of jobs currently managed by the shell
\l : the basename of the shell's terminal name TBI not really understood, e.g. on laptop this gives 'tty2'
\n : a carriage return and line feed
\r : a carriage return
\s : which shell
\T : the current time (12-hour HH:MM:SS)
\t : the current time (24-hour HH:MM:SS)
\@ : the current time (12-hour a.m/p.m format)
\u : username
\v : shell version
\V : the release of bash, including patch level, etc.
\w : pwd
\W : basename of pwd
\# : the command number of the current command
\! : the history number of the current command
\$ : if the effective UUID is 0, print #, otherwise $
\nnn : character code in octal
\\ : print a backslash
\[ : begin a sentence of non-printing characters
\] : begin a sentence of non-printing characters

=> TBI: On the meaning of '${debian_chroot:+($debian_chroot)}' in PS1, read the following thread: https://askubuntu.com/questions/372849/what-does-debian-chrootdebian-chroot-do-in-my-terminal-prompt

**o Directory search path and variables (CDPATH, etc.)**
o the key variable is CDPATH => it holds colomn separated list of directories
o it augments functionaliy of cd command
o by default NOT set
o The point: when you type cd <dirname> , the shell looks by default only for subdirectory <dirname> in the current directory
    oo Three exceptions: <dirnname> starts with / , ./ , or ../
o Basically, if you set CDPATH, you give a list of directories for shell to look for. The list may or may not include the current directory
o The curtent directory is added to CDPATH as an empty string, e.g. 
    CDPATH=:<some-other-path>
    => This setting includes the current directory, and <some-other-path>
    => <some-other-path> can be relative, not necessarily absolute, e.g. '~/temp'
o CDPATH is not that powerful feature, since the working habits/dirs change rather frequently
o In this context: set option cdable_vars for shopt command => TBI see top of page 74

**Miscellaneous variables**
o Status variables, not used for customization (like most of previous examples)
HOME
SECONDS => number of seconds since the shell was invoked. AB: Each shell level apparently maintains its own counter. AB: The top counter is not affected with lower levels.
                           Can be reset to another number. But once unset, it uses its special meaning.
BASH
BASH_VERSION
BASH_VERSINFO => array version of BASH_VERSION
PWD
OLDPWD => previous directory before the last cd command
Examples: 
echo $BASH_VERSION
4.3.11(1)-release
echo ${BASH_VERSINFO[*]}
4 3 11 1 release x86_64-pc-linux-gnu

**Environment variables (export)**
o By default, only a special class of shell variables is known to all subprocesses => environment variables 
o Any variable can become environment variable, just export it
o You can either:
    VAR_1=10
    VAR_2=20
    export VAR_1 VAR_2
or
    export VAR_1=10
    export VAR_2=20
o By default all variables will be exported, by set -a or set -o allexport
o It is also possible to define variables to be in the environment of particular subprocess (command) only:
   <varname>=<value> <command>
   oo You can put as many assignments before the command (only of you set -k , you can also put it after the command) as you want (no need for ; to separate them)
VAR_1=10 VAR_2=20 sleep 10s &
[1] 13044
echo $VAR_1
<nothing-is-printed--VAR_1 is only known in the subprocess>
   oo On the other hand:
VAR_1=10; VAR_2=20 sleep 10s &
echo $VAR_1
10
   oo Example: editor works only with particular terminal setting TBI
   TERM=<whatever> emacs filename.log
   => the important point is that TERM's setting in the current shell will NOT be overwriten 
o Where to store definitions of environment variables? In .bash_profile
    => A lot of them sometimes are also stores in system-wide /etc/profile file
o AB: export is equivalent to declare -x
export VAR=44
\# same as:
declare -x VAR=44
o How to find out which variables are environment variables? Type export without arguments, or export -p
o How to remove exported variable from environment? 
export -n <var-name>
o Some env variables have been used so widely by applications, that they become standard variables across different shell environments:
oo some standard variables:
COLUMNS  :  The number of columns your display has. It is being reset automatically by bash, after terminal is resized. WONDERFULL!!!! At maximum, 205!! By default, 80
EDITOR  :  Pathname of oyur text editor
LINES  :  The number of lines your display has. It is being reset automatically by bash, after terminal is resized. WONDERFULL!!!! At maximum, 58!! By default, 24
SHELL  :  pathname of the login shell (BASH is the pathname to the current shell! SHELL and BASH may or may not be the same). 
                  => Typically set by the process that invokes the loging shell, like login or rshd (if you are logged in remotely). 
                  => bash sets it only if its not already set
                  AB: TBI not reliable, when i enter 'fish' , SHELL is still pointing to /bin/bash
                  SO: The caveat for this one is that if you launch a shell explicitly as a subprocess (e.g. it's not your login shell), you will get your login shell's value instead
TERM  :  The type of terminal you are using

ooo Terminal types
o The variable TERM is vitally important for any program that uses your entire screen or window, like a text editor. Examples: vi, emacs, more, etc.
o The value of TERM must be a short character string with lowercase letters that appears as a filename in the terminfo database.  In my case, it is in /usr/share/terminfo
ls /usr/share/terminfo
1  3  5  7  9  A  c  e  f  h  j  l  m  n  o  P  Q  s  u  w  X
2  4  6  8  a  b  d  E  g  i  k  L  M  N  p  q  r  t  v  x  z
=> What are all those subdirectories with single-character names? Each file describes how to tell the terminal in question to do certain common things like positioning the cursos on the screen, go into reverse video, scroll, insert text. The descriptions are in the binary form (not readable by humans)
o Example: An xterm terminal window under the X Window System has a description in /usr/share/terminfo/x/xterm
o Example (SO): If a program want's to display colored text, it must first find out if the terminal you're using supports colored text, and then if it does, how to do colored text. The way this works is that the system keeps a library of known terminals and their capabilities. On most systems this is in /usr/share/terminfo (there's also termcap, but it's legacy not used much any more).
o Example: tput setf 4; echo hi. # This will get the setf terminfo capability and pass it a parameter of 4, which is the color you want.
o TBI read the great SO entry: https://unix.stackexchange.com/questions/93376/which-terminal-type-am-i-using
o TBI re-read again top of page 78
o TBI The X Window System

ooo Other common variables
o some programs, like mail, need to know which type of editor you would like to use. Most likely, they will default to ed, unless you set EDITOR




