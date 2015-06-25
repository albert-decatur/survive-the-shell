survive the shell
====================

Bash is a programming language for getting stuff done stupid fast.

It's not for software development, but it can save your life.

With bash, you run utilities to do stuff.

You can send the output of one utility to become the input of the next utility using **pipes**.

You can feed files to utilities and redirect the output of utilities to files.

You now know the essence of Bash.




##utils


## simple tricks to help you survive the shell

Note there here, "^" means "hit control", and "+" means "then hit . . .".

1. when you want to cancel something hit ^+c
1. when you want to exit a terminal, hit ^+d or type exit
1. when you want to exit a program that you can't seem to exit
  1. just type "q".  works for the pager "less"
  1. type ":q" if you're in the text editor vim
  1. type "\q" if you're in a SQL shell
1. when everything is totally **borked** - meaning there's a total **snafu** - just type "reset".  Should set your terminal back on the right path.


##comments

##hidden files and directories

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
One great pager is "less", which uses these keybindings:

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
Pipes pass the output of one util to become the inpput of the next utils.

It looks like this . . .

```bash

```


##redirection



##sed, awk, grep

Sed, awk, and grep are super helpful utilities.
You can use them this way:

* sed subsitutes things according to a pattern
* awk handles tables
* grep pulls out records according to a pattern


## text editors
... are your friend.
They let you write configure files that control how utilities run.
Configuration files have names like these:

* ~/.bashrc
* ~/.vimrc
* ~/.config/surfraw/conf


##script writing
Just add a shebang!
Here's how . . . 
Write this into a text file called "foo.sh".
Note the Windows users: file extensions are basically just a clue to people and computers about what the file **might** contain.  They don't actually dictate what's in a file, and could actually be anything.
```bash
#!/bin/bash

ls
```
That first line is called a *shebang* or *crunchbang*.
You use it for other programming languages too, like you might use this for ruby: "#!/usr/bin/env ruby".
Now make your text file into an executable script with this . . .
```bash
chmod +x foo.sh
```
And run it like this!
```bash
./foo.sh
# or this, but you don't need to mention bash since the crunchbang line mentions it
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

* when used as authentication there's no need to write out your username and password
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
If you want someone to be able to ssh into your localhost then put their public key in this file (that includes you!).
One public key per line!


##bash tricks

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
You PATH is just a list of diredctories that hold executables you can use from anyplace.
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

###reverse search
###find
###grep -r

##HERE documents
HERE docs print out multiline chunks of text.
Here's how they work:
```bash
cat <<EOF
yo check it
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
Not every langauge or tool will use regex the same way.

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
  * great tab autocompletion, endless features

##glossary
word|meaning
---|---
terminal|another name for the shell / command line / command prompt.  This is the magic black box where you issue super powerful commands to your system
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
standard streams|the inputs (STDIN), ouptuts (STDOUT), and errors (STDERR) produced and used by commands
directory|just a "folder" from Windows speak

##resources

* [commandlinefu](http://www.commandlinefu.com/commands/browse)
* [stackoverflow](http://stackoverflow.com/questions/tagged/bash)
* [nixCraft](http://www.cyberciti.biz/)
* [kmandla's blog](https://kmandla.wordpress.com/)
