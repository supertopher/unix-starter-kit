![image](https://raw.githubusercontent.com/supertopher/unix-starter-kit/master/TMNT.png)

# Unix

### Understanding these concepts will make the rest of your programming life easier.

We are going to quickly cover a number of UNIX concepts.  Understanding these concepts will make you a better developer, regardless of the language you write in.  You will be able to install new languages and tools, diagnose your own postgres issues, and rescue other people whose terminals are mad at them.

By the end of these challenges you should be comfortable:

* Using the man pages to effectively understand commands you don't know in UNIX
* Understand how to use the pipe operator
* Manipulating the PATH
* Installing and debuging new executables within your own environment


## Release Zero
-------------


*Learn the most important command in UNIX.*

![image](http://imgs.xkcd.com/comics/rtfm.png)

Unix was created in the 70s.  At this time internet access was virtually non-existent.  You couldn't google answer. You can't check the project's github page.  Yet, the engineers crafted a working solution for teaching new users without either of those tools: Man pages.  Using the ```man``` command you can read the manual for virtually all UNIX tools.  This solution works so well that googling the answers to questions answered in the man pages is often virtually impossible.  People who write shell don't copy things from the manual to the internet.  You would never need to; the answer is right at your fingers with man pages.

All man pages will be opened in a tool called ```less``` which is a UNIX program that aids in reading information that is too large for the standard output.  (IE: man pages)  To be clear, when you type ```man ls``` it opens the manual for ```ls``` in the unix tool ```less```

Let's use the ```man``` tool to understand ```less```

try the command ```man less```

You should see before you a brief description of the command followed by an explanation of it's options.  You can use regex to search any file that is open in less by typing ```/``` followed by any regex search.  This search can be as simple as typing ```pattern``` to find the fist occurrence of the word pattern in the manual.  This is a quick way to understand what command flags (sometimes called switches) are doing for any UNIX command. The ```-l``` in the command```ls -l``` is an example of a flag.

You have probably been told at some point during your education here to use ```ps -A | grep ruby``` or ```ps aux | grep ruby``` to find a ruby process that is running the background.  If you don't know what those commands are doing ```man``` is a great place to find out what those commands you are typing actually do.

Try using ```man ps``` in the terminal.  Skim this document and determine the following

What is the ```ps``` command doing?

What do the flags on ```ps``` do?  (Flags are the options that come after a command ie the ```-A``` in ```ps -A``` and the ```aux``` in ```ps aux```)  Try running some variations on this command using the information you have gleamed.

Make your ```ps``` output more readable by adding the flag that will change the command names listed from the full command line to simply the executable name

Once you find this flag the output of this command should change from what ```ps -A``` normally gives you to look more like this:

```
11235   ??  Ss     0:00.05 ocspd
11260   ??  S      0:00.11 CVMCompiler
11369   ??  Ss     0:07.84 com.apple.WebKit.WebContent
11621   ??  S      0:00.03 mdworker
10041 s000  Ss     0:00.06 bash
11733 s000  R+     0:00.00 ps
11463 s001  Ss+    0:00.02 bash
```

Having mastered the ```man``` command this guide will no longer explain new commands to you.  If you don't know what the command does look it up in the man pages until you do.


## Release 1
-----------
*The PATH*

![image](https://raw.githubusercontent.com/supertopher/unix-starter-kit/master/path.jpg)

The path is a list of directories that UNIX shell looks in for executables.  Let's examine the path of the computer you are currently on.  PATH is just a variable in unix.  As with any variable we can ```puts``` the content of that variable into our interactive shell.  In ruby land to see a variable you can do things like ```puts mah_sweet_variable```.  When you run the application you will see the contents of that variable.  The shell is exactly like running a session of IRB in that you are always writing code that is immediately evaluated when you press enter.

In unix we use ```echo``` instead of puts.  Try this now with the command ```echo $PATH```  the ```$``` indicates to the shell that you want the code that comes next to be evaluated before the rest command in completed.  This helps the shell understand the difference between human speech and code that needs evaluation.  This ```$``` can be used in any unix shell to force the shell to evaluate something first.

You may have noticed that this string of output is basically unreadable.  The syntax that separates lines in the PATH variable are ```:```s.  Each ```:``` denotes the end of one line and the start of a new one.  We can replace those ```:```s with new line characters using UNIX.  This is all part of the beauty of UNIX commands, they all share a common plain-text output which makes communication between UNIX tools very easy to create.

Using the ```tr``` tool we format the output to be much more readable.  try running ```echo $PATH | tr : \\n```.  This should give you an output that looks something like this.

```
/usr/local/bin
/usr/local/sbin
~/bin
/Users/topher/.rbenv/bin
/Users/topher/.rbenv/shims
/usr/bin
/bin
/usr/sbin
/sbin
```

This list of folders are the locations of executables in your computer that the shell will look through.  Every time you type any command in unix ie: ```ps ls cd ruby bundle``` literally every command you type is in one of these folders.  When you type anything for example ```ruby``` unix will start at the top of this list and work it's way down looking for a command called ```ruby```.  Unix will stop after it finds any match.  Thus you can prioritize tools of the same name by putting those things earlier in the path.  For example you never uninstalled the system version of ruby, yet when you type ```ruby``` you get the version that RVM gives you instead of the version that shipped with OSX.

Unix comes with a tool to help you debug issues with the path called ```which```.  See which ruby your computer is currently running with ```which ruby```.  The result of which tells you where unix found the command that you ask it about.  This output of this command should tell you that you are using version of ruby provided by rbenv.  We will create an example of this together in the next section.

## Release 2
-----------

*Create a new executable and add it to your path*

Create a directory using ```mkdir```

```touch``` a new file called ```ls```

Edit the new file using your editior of choice.

Add to this file a command that has some interesting output for example ```echo My Cohort Runs DBC!!!!!111one```.  Save this file and try to run it locally.  When you want to execute any program not found in your path (such as the one we just made) you need to tell unix the path to that program not just the name of the program.  Thus we can type the full path to the command ie: ```/Users/topher/Desktop/super-best-unix/ls``` or we can use ```.```.  Obviously typing the complete path to a folder you are already in is annoying.  You can always shortcut to your current working directory with a simple period.  So we can type ```./ls``` and it will have the same effect as typing the full path.  Spoiler Alert: ```bash: ./ls: Permission denied``` is going to happen to you.

In the windows world we know that a file is a program because the filename ends in ```.exe``` in unix there are a series of bits that tell the computer if a file can be executed by a given user.  In order to execute this file we must flip the executable bit on.  Shockingly unix has a tool for that.  We can use ```chmod```to modify this bit.  Use the man pages or the googles to determine how to do this.  Hint: the syntax to REMOVE read permission from the file we just created you could run ```chmod -r```.

You can determine if you have successfully made the ls program when typing ```./ls``` yields the echo output you wrote earlier.

We are lazy.  Typing that ```./``` everytime we want to run our little ls is super annoying.  Let's add it to the path with all of our other cool programs.  Below are some examples of adding directories for rbenv and homebrew to the path.

```
PATH="$HOME/.rbenv/bin:$PATH"
PATH="/usr/local/bin:/usr/local/sbin:~/bin:$PATH"
```

Note that each path location is delineated by a ```:```.  Additionally these additions to the path are made non-destructive because the END of each addition the variable ```$PATH``` will add the existing path back to the path variable.  The keeps the existing path intact when adding new things to it.  You need to add ```.``` to the beginning of you path for this part of the challenge.  Please note that shell variables hate whitespace.  Whitespace tells shell to look for a new command.  Don't use spaces.  For example:

```
TOPHER="You can have spaces in strings, thats cool, but not outside strings :/"
echo $TOPHER
```

When you have succeeded you will be able to type ```ls``` in the folder where your custom ```ls``` is located and see the output of your ```echo```.  Additionally the command ```echo $PATH | tr : \\n``` should look something like this

```
.
/usr/local/bin
/usr/local/sbin
~/bin
/Users/topher/.rbenv/bin
/Users/topher/.rbenv/shims
/usr/local/bin
/usr/bin
/bin
/usr/sbin
/sbin
```

Congratulations! You can now manipulate a path (and troll people!)

However, if we want this setting to exist outside of the current session we have to add this PATH addition to the shell initialization files.  Most commonly for bash this is ```.bash_profile``` and in ZSH is ```.zshrc```.  These files are just a list of commands than run when you start your shell... **Every time** you start your shell.

## Release 3

*Bringing all together with something completely different*

![image](https://raw.githubusercontent.com/supertopher/unix-starter-kit/master/diff.jpg)

Lets install python and a python version manager.  As with any command line installation in OSX we should start with ```brew```.  There is a project called ```pyenv``` for python version management that functions almost identically to ```rbenv```.  This is the [Pyenv Github Page](https://github.com/yyuu/pyenv).  The github page of projects is a great place to find information on installation and trouble shooting.  They may not always have the answer, but it is best to check with the authors before consulting random people on stack overflow.

Let's install pyenv with ```brew```.  You should always read the output after a brew install to see if there are post installation notes.  However in this one case: Do **NOT** follow the post installation instructions.  We are going to add the path for python manually.  To start let's see what python this system is using now with ```which```.  You should see output similar to:

```
/usr/bin/python
```

This is the system version of python.  Just like the system version of ruby, you should never modify this version.  The system depends on these specific versions of ruby and python for standard scripts.  Changing them by adding gems for example will cause errors for system scripts.  This is one of the reason we use version managers for our languages.  Let's see what our options for installation are with pyenv.  Type ```pyenv``` and read through the list of commands.  Many commands will show help when insufficient or incorrect arguments are given.  Using this help list determine what command will help you install a version of python.  Then type that command and read it's help output to try a see a list of available versions of python to install.  Once you have seen the list install python version 3.4.0.  Set this version of python to be used globally using the ```pyenv global``` command

After this installation we can check to see if we are now using a new version of python with ```which``` again.  Sadly, we are still using system python.  To fix this we have to add pyenv's executable directories to our path.

According to the project's github readme the default root installation directory for Pyenv is ```~/.pyenv```.  This directory stores it's python executables in a folder called ```shims``` We must add this to our path using the shell initialization files (again ie: ```.bash_profile```)  When adding home directories to the profile it is considered good practice to use ```$HOME``` instead of ```~```.  If you cd to each of these you will find that they both resolve to the same place.  Add python to your path non-destructively using ```.bash_profile```.

When you have succeeded the output from which python should read ```/Users/topher/.pyenv/shims/python```