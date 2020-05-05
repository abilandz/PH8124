### Special configuration files in Bash

In this section we summarize the important configurations files which have a special meaning to **Bash**. Configuration files which the user can directly edit acquire their special meaning only if they are placed in the user's home directory, otherwise **Bash** will not find and execute them. To stress this out, we have prepended ```~``` to the name of user's configuration files below. By convention, the name of all configuration files in the home directory begins with '.' (dot), which means that **ls** will not list them by default.

***User's configuration files***

* ```~/.bash_profile``` : this configuration file is only executed by **Bash** each time you log in on computer. There are two synonyms for this file:  ```~/.bash_login``` and ```~/.profile``` . They are executed at login only if ```~/.bash_profile``` is not present in your home directory. The files ```~/.bash_profile``` and ```~/.bash_login``` can be read only by **Bash**, while ```~/.profile``` is read also by some other shells, e.g. **sh** and **ksh**

* ```~/.bashrc``` : this configuration file is read with the highest priority when you open a new terminal, or when you start a new _subshell_ (covered later in the lecture). This file can be read also at login only if you add a line ```source ~/.bashrc```  in ```~/.bash_profile```  


* ```.bash_logout```: Executed every time when login shell exits. Use case: a) Delete all temporary files; b) Calculate how much time I've been logged in, etc.

***System wide (default) configuration files***

If by accident you have deleted your personal configuration files in your home directory, as a backup solution you can always rely on two system wide configuration files, which you cannot edit directly without having the administrator privilages:

* ```/etc/profile``` : the default, system wide, configuration file which is read at login. It is read before ```~/.bash_profile```, ```~/.bash_login``` and ```~/.profile```. This means that you will always have some default settings enabled after you login in the computer, whether or not the configuration files in your home directory exist
* ```/etc/bash.bashrc``` : the default, system wide, configuration file which is read each time you open a new terminal or start a new subshell. It is read before ```~/.bashrc```. This means that you will always have some default settings enabled after you open a new terminal or start a subshell, whether or not your ```~/.bashrc``` with your personal settings in exists or not
