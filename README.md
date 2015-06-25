survive the shell
====================

Bash is a programming language for getting stuff done stupid fast.  
It's not for software development, but it can save your life.  

With bash, you run **utilities** to do stuff.  
You can send the output of one utility to become the input of the next utility using **pipes**.  
You can feed **files** to utilities and redirect the output of utilities to files.  
You now know the essence of Bash.  

##utils

On a Debian Linux based system like Ubuntu, you can install a ton of awesome utils as simple as . . .

```bash
sudo apt-get install curl axel lftp git-core imagemagick poppler-utils unrar unzip htop nmap parallel feh rsync vim gt5
```

In this example, you're asking the package manager apt to install a list of packages.  
Notice how the list is space separated.  
In bash every time you enter a space it interprets it as a new argument.  
Essentially, bash commands take the form

```bash
command-name --some-flag first-argument second-argument "third argument"
```

Flags are options for commands.  
They'll be explained soon!  

If you're a Mac user, try [homebrew](http://brew.sh/) as your package manager.

## simple tricks to help you survive the shell

Note that here, "^" means "hit control", and "+" means "then hit . . .".

1. when you want to cancel something hit ^+c
1. when you want to exit a terminal, hit ^+d or type exit
1. when you want to exit a program that you can't seem to exit
  1. just type "q".  works for the pager "less"
  1. type ":q" if you're in the text editor vim
  1. type "\q" if you're in a SQL shell
1. when everything is totally **borked** - meaning there's a total **snafu** - just type "reset".  Should set your terminal back on the right path.


##comments

Every (every?) programming language has the ability to leave comments.  
In bash we use the pound symbol, which you may know as the hash-tag (#).
For example

```bash
# this is a comment.  it does not run!
ls # that was code.  it does run!
# anything on a line after a # sign does not run in bash!
```

##hidden files and directories

Sometimes files and folders (called "directories") are hidden from you.  
What could they be hiding??  
Usually just stuff you don't need to see every day, like configuration files.  
We'll talk about those later too.  

Hidden files and directories start with a period.
Any file or directory that starts with a period is hidden, which means it won't normally come up when you "ls".
However, you can always see it if you "ls -a".
Give it a try!

```bash
# go to your home directory using the ~ special character
cd ~
# look at your files in that directory
ls
# can't see those hidden files!
# let's try ls's -a flag to see **all** teh files!!
ls -a
## haha! take that bash shell. now we know your secrets.
```

One thing you'll notice when you "ls -a" are these weird directories called "." and "..".  
Those are "current directory" and "directory above current directory".  
Note that when you "ls" it's really equivalent to "ls .".  
This is true of many commands because typically you execute commands on stuff in the "working directory".  

##command flags ("options")
Flags allow the user to turn on and off various util options, or to point utils to inputs and name outputs.

```bash
# list all files, including hidden ("dot") files
ls -a
# tell me how big a certain file is *using kilobytes, megabytes, etc*
du -h myfile.txt
# tell me how much free memory the system has *using gigabytes*
free -g
```

##man pages and the -h flag

Always remember to Read the F\*\*\*ing Manual (RTFM)!
You can access a command's manual using the man command.
Like this!

```bash
man ls
```

Don't panic!
Just hit "q" to quit this view.
It's actually just an instance of the pager "less".
Pagers are really important!

Many newer commands don't have a manual you can access with "man".
These usually let you use a flag like --help or -h.
For example . . .

```bash
cd -h
# or
cd --help
```

##pagers

Pagers let you see one **page** of text on your terminal at a time.
This is crucial because outputs of commands could potentially be millions of lines long, or even infinite!
One great pager is "less", which uses these key bindings:

* q to quit
* gg to go to top
* G to go to bottom
* / to start a search
* when doing a search, n to go to next entry
* when doing a search, N to go to previous entry

Because you've been so good, here's a fun example of an infinite output.
Just replace "foo" with anything you want:

```bash
yes foo
```

Hit control and then c to cancel!!
Otherwise it will go on indefinitely.

##pipes

Bash is all about utils and pipes.
Utils each do one thing well.
Pipes pass the output of one util to become the input of the next utils.

It looks like this . . .

```bash
# print "foo" forever and ever.  no, wait!  use head to just take the first ten you print
yes foo | head -n 10
```

Yes!  
You stemmed the vile tide of foo foo foo that was headed our way by just taking the first ten instances.  
Nice work saving us back there.  

##redirection

Remember that the whole points of bash is to move data between pipes!  
Files are just a place to keep data and code until we need to call on it.  
**Redirection** lets us 

1. dump the output of a command into a file for later use
1. dump an existing file into a command

It looks like this

```bash
# redirect some text into a file
# hope you don't have a file named foo because we're about to overwrite it with this redirection!
# note that using echo's -e flag, "\n" is interpreted as newline so this is a four line text file
echo -e "this\nis\nsome\ntext" > foo
# now let's go a little crazy.
# redirect that file into a while loop!
while read line; do echo $line HEY; done < foo
```

Output should be

this HEY  
is HEY  
some HEY  
text HEY  


## append

Just like redirect except we don't overwrite the file we redirect to.

This is hella useful.  
Trust me.  

Looks like this

```bash
# use redirection to make a file "foo" with just the word "bar" in it
echo "bar" > foo
# now use append and a for loop to add stuff to the end of the file foo, one line at a time
for i in 1 2 3; do echo $i >> foo; done
# now check out your new and improved foo!
cat foo
```

Should look like

bar  
1  
2  
3  

##sed, awk, grep

Sed, awk, and grep are super helpful utilities.
You can use them this way:

* sed substitutes things according to a pattern
* awk handles tables
* grep pulls out records according to a pattern


## text editors
... are your friend.
You need them to write text files, which are kind in \*NIX land.
For example, configuration files that determine how programs run, or scripts that do tasks for you.

The best text editor ever made is . . . a matter of heated debate!
I like vim.
Here's a [game](http://vim-adventures.com/) to help you learn it!

Vim is a **modal** text editor which means it has multiple modes.
It's important to always know which mode you're in.
Good luck!

To open a file with vim just

```bash
# let's make a bogus file with redirection
echo "some text" > foo
# open that file with vim
vim foo
```

To simply exit vim type ":q", which means "quit".
To save and exit, type ":wq", which means "write to file and quit".
The colon lets you enter commands when you're in command mode, which is the default.
Hit "i" to enter insert mode, which is what you're used to for entering text.
Hit escape to leave insert mode and go back to command mode.

Or you could just use any old graphical text editor for now.
That's cool too.

## configuration files

Often called "config" files, these text files control the behavior of programs like web servers or even small utilities.
Configuration files have names like these:

* ~/.bashrc
  * controls how your bash session works
* ~/.vimrc
  * controls how the text editor vim works
* /usr/share/postgresql/9.3/pg_hba.conf
  * controls how postgreSQL 9.3 works
* /etc/ssh/sshd_config
  * controls how OpenSSH server works

Sometimes you'll see the suffix "rc" which stands for "run commands".  
Other times the file will just be named "config" or "foo_config" or "foo.conf".   
Config files don't make changes to a program's behavior immediately.
You have to restart the program the config file controls to let those changes get loaded up.
For example, if you change your ~/.bashrc you can either start a new terminal or you could 

```bash
source ~/.bashrc
```

And if you change your postgreSQL config you can

```bash
service postgresql restart
```

As you can see, there is no single way

* that config files are named
* where config files live in the directory hierarchy
* or how to restart a program whose config file you just changed 

##script writing
Just add a shebang!
Here's how . . . 
Write this into a text file called "foo.sh".
Note the Windows users: file extensions are basically just a clue to people and computers about what the file **might** contain.  They don't actually dictate what's in a file, and could actually be anything.
```bash
#!/bin/bash

ls
```
That first line is called a *shebang* or *crunch bang*.
You use it for other programming languages too, like you might use this for ruby: "#!/usr/bin/env ruby".
Now make your text file into an executable script with this . . .
```bash
chmod +x foo.sh
```
And run it like this!
```bash
./foo.sh
# or this, but you don't need to mention bash since the crunch bang line mentions it
bash foo.sh
```
Of course, all it does is run the commands you wrote in the text file foo.sh in the order you wrote them, exactly as if you pasted them into the command line.
But scripts have some serious advantages over running commands at the command line:

* user arguments
  * positional parameters or flags
* can be called by **other** programs, for example cron


##ssh 
###key encryption
Key encryption is better than passwords.
Here's why:

* when used as authentication there's no need to write out your user name and password
* if you manage to keep your private key secure it's much safer

###/etc/ssh/sshd_config
###/etc/init.d/sshd
###~/.ssh/
Find your key pair here.
```bash
ls ~/.ssh
```
It will look like this:

* id_rsa
* id_rsa.pub

Share the public (".pub") key.
Never share the private key!
People with your private key can pretend to be you and decrypt anything that someone sends you.

Under this directory you'll also find authorized_keys.
If you want someone to be able to ssh into your local host then put their public key in this file (that includes you!).
One public key per line!


##bash tricks

1. double quote a variable to preserve whitespace
  1. this is crucial for stuff like a TSV!

###tab completion

When you're in the bash shell and don't know what you might want to type next just hit TAB once or twice.
Go ahead.
Give it a try!
If you type nothing and hit tab twice, bash will just tell you every command in your PATH.
That's because you always start by entering a command's name, followed by its parameters.

### PATH

Go ahead and 

```bash
echo $PATH
```

Note that the list you get back is colon separated - each colon means a new directory is about to be mentioned.
You PATH is just a list of directories that hold executables you can use from anyplace.
For example, if you

```bash
ls /bin/
```

you get back a list of executables kept under the directory /bin/.


### \*NIX directory structure

\*NIX is just a cutesy way of saying "anything like UNIX", like Linux.
It uses wildcards, which are just special characters for pattern matching.
\*NIX systems - like Mac OS X - have a folder hierarchy that starts with "/" at the top.
Everything is under "/", including attached devices like network drives and USB sticks!
Look for those external devices under "/media/".
Look for users' folders under "/home/".
There are some very special stuff in the hierarchy like "/tmp/" which is wiped on every shutdown, and "/dev/null" which is a black hole of nothingness.  
You've been warned.

###reverse search
###find
###grep -r

Try a grep recursive search!  
**Recursion** is another great concept in \*NIX systems.  
It just means to keep going down and down as far as you can go.  
For example, if a directory has a directory in it, that has another directory in that, you can use recursion to search for patterns in **all** the files in all those directories.  

Let's look for every instance of the letter "a" in all files under your home directory.
There's going to be a ton of them so we'll pipe the output to a pager!

```bash
# find all lines that contain the letter "a" in any file in or anywhere under your home directory
grep -r "a" ~ | less
```

Now here's a little lesson in pipes.
Let's say we don't care about the matching text but just the files that contained the text:

```bash
# find all lines that contain the letter "a" in any file in or anywhere under your home directory
# note that the -i flag for grep makes the match case insensitive, so that "a" is considered the same as "A"
grep -ir "a" ~ |\
# now chop off everything but the names of the **files** that these lines appear in
# this regular expression (see appropriate section in this README!) matches only from beginning of line to right before the first colon
grep -oE "^[^:]*" |\
# now take just the unique filenames
sort -u
```

Note that we used a backslash (\) to keep executing the same line of bash but to write about it over multiple lines.

##HERE documents
HERE docs print out multi line chunks of text.
Here's how they work:
```bash
cat <<EOF
this is a HERE doc
alright!
EOF
```

##exit status
The exit status of a command tells you whether it succeeded, or if it failed how it failed.
An exit status of "0" is all good.
Anything else signals an error and demands your attention!

## ~/.bashrc
Your Bash "run commands" file contains instructions that are run each time you log into Bash.
Aliases and functions are awesome shortcuts you can write once you realize you've been typing out the same stuff over and over into the terminal.

If you make changes to your ~/.bashrc and want them to take affect without logging in again just
```bash
source ~/.bashrc
```

##GNU screen
GNU screen lets you run lots of terminals in one.
If this isn't obviously awesome now it will be soon.

GNU screen commands:

```bash
# start a new screen session
screen
# detach a screen session - this is amazing.  you can re-attach anytime
screen -d
# *re*-attach a previously detached screen session
screen -r
```

Check out these commands to control screen once you're in a session:
(Note that "^" means hitting the control key and "+" means hitting the next key real fast)

* ^a+c
  * "create" a new terminal window
* ^a+n
  * go to the "next" terminal window
* ^a+p
  * go to the "previous" terminal window
* ^d
  * exit this terminal window
* ^a+K
  * kill the current window and everything in it - when things get out of control!

There are lots of alternatives to GNU screen, like [tmux](http://tmux.github.io/).


##regular expressions ("regex")
Regular expressions are a set of rules for matching text according to a pattern.
This is absurdly awesome.
But beware!
Not every language or tool will use regex the same way.

Don't go around trying to solve every problem with regex.
It's powerful but dangerous and cryptic.

Examples:

```bash
# note that the -E flag for grep isn't strictly needed in each case but it doesn't hurt
echo "start is the word that starts this line" | grep -E "^start"
echo "end is the word at this line's end" | grep -E "end$"
echo "these words must be whole but can appear in any word order" | grep -E "\bwhole\b|\bwords\b"
echo "period matches any one character, except in brackets which means real period." | grep -E ".[.]"
echo "does this match everything but the question mark?" | grep -E "[^?]"
echo "this matches numbers like 90 and 4, but see how the decimal doesn't match in 4.1?" | grep -E "[0-9]+"
echo "this matches numbers like 4.1 and -4.1" | grep -E "[0-9.-]+"
echo "this only properly matches two digit numbers like 10 and not 110 or 1" | grep -E "[0-9]{2}"
echo "this matches everything in this string but 6 but do you know why?" | grep -E "[A-Za-z0-9.?-]{2,}"
```

No lesson on regex is complete without URL matching examples!

```bash
# first get some results from duckduckgo
curl -s "https://duckduckgo.com/html/?q=foo" |\
# then grab just strings that look like URLs.
# in this case, we're looking for "http://" at the beginning and then "." and then something three characters long.
# this definitely does not match all URLs.
# note grep's -o flag means keep only match
grep -oE "http://.*[.][a-z]{3}"
```

##other shells
Bash is the default shell on most Linux/Mac systems, but
there are lots of shells besides Bash.
Some other shells include:

* dash
  * simpler than Bash
* csh
  * C language inspired shell
* ksh
* zsh
  * great tab auto completion, endless features

##glossary
word|meaning
---|---
terminal|another name for the shell / command line / command prompt.  This is the magic black box where you issue super powerful commands to your system
nix, \*NIX, POSIX| all references to UNIX-like systems, for example Linux or Mac OS X
field|column
record|row
record separator|the character that distinguishes units of analysis from each other, usually newline ( "rows" of data )
field separator|the character that distinguishes attributes of a unit of analysis, usually comma (CSV) or tab (TSV)
*plain* text|text you can read from the terminal ( *not* Word docs! ). Note that because of character encoding there's really no such thing as *plain* text
character encoding|set of systems used to turn computer stuff (bits) into "plain" text (letters, numbers, etc)
carriage return|mostly harmless character that sometimes gets used in excel exports instead of line feeds (proper UNIX style newlines)
line feed|in UNIX style, the classic record separator usually associated with the 'Enter' key
ASCII|classic simple character encoding really only good for English language
UTF-8|awesome modern character encoding good for every language
utils|commands (**utilities**) that do one job and do it well
pipes|tool to send the output of one command to become the input of the next command (written like this: "|")
standard streams|the inputs (STDIN), outputs (STDOUT), and errors (STDERR) produced and used by commands
directory|just a "folder" from Windows speak

##cultural glossary

word|meaning
---|---
hack|a quick but often fragile solution to a problem
munging, to munge, sometimes "wrangling"|acrobatic data manipulation
grok|to understand truly and deeply
RTFM|a suggestion to consult existing help, literally Read the F\*\*\*ing Manual
NB|nota bene - meaning "pay careful attention to the following"

## great concepts in \*NIX

* **utilities** with a single simple purpose
* **pipes** to move data between utils
* **redirection** to and from utils
* **recursion** into directories

## tips on dealing with frustrating characters when data munging

```bash
cat foo | tr -d '\000' # gets rid of NULL bytes
cat foo | tr -d '\r' # gets rid of carriage returns
cat foo | tr '\r' '\n' # replaces carriage returns with line feeds
```

##resources

* [commandlinefu](http://www.commandlinefu.com/commands/browse)
* [stackoverflow](http://stackoverflow.com/questions/tagged/bash)
* [nixCraft](http://www.cyberciti.biz/)
* [kmandla's blog](https://kmandla.wordpress.com/)
