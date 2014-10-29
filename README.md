Do-ers Guide to Bash
====================

Do-ers are people who get things **done**.
Here's how to be a do-er with Bash.

Bash is a programming language.
Bash is a glue language.
Glue holds stuff together.
Utilities do stuff to inputs and spit out outputs.
You can send the output of one utility to become the input of another using pipes.
When you're piping you get stuff done **fast**.
When you're done piping you can redirect to file.
You now know the essence of Bash.

##utils
Utilities ("utils") are commands that get stuff done.
```bash
# list files
ls
# tell me how big a certain file is
du myfile.txt
# tell me how much free memory the system has
free
```

##comments
Comments start with a pound ("#").
Anything after a comment is not executed by the shell.
Remember, your shell is Bash!
You'll notice them in the utils section above.

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

##Pipes


##Redirection

##sed, awk, grep

Sed, awk, and grep are super helpful utilities.
You can use them this way:

* sed subsitutes things according to a pattern
* awk handles tables
* grep pulls out records according to a pattern

##vim 
Vim is a text editor.
It's really good.
It has two modes.

##script writing
Just add a shebang!
###shebang line

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
###reverse search
###file extensions are meaningless
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
Anything else is bad!

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
(Note that "^" means hitting the control key)

* ^a+c
  * "create" a new terminal window
* ^a+n
  * go to the "next" terminal window
* ^a+p
  * go to the "previous" terminal window
* ^d
  * exit this terminal window


##regular expressions ("regex")
Regular expressions are a set of rules for matching text according to a pattern.
This is absurdly awesome.
But beware!
Not every langauge or tool will use regex the same way.

Don't go around trying to solve every problem with regex.
It's powerful but dangerous and cryptic.

Examples:

```bash
grep -E "^start"
grep -E "end$"
grep -E "\bwhole\b \bwords\b"
grep -E "[^(not this)]"
grep -E "[0-9]+"
grep -E "[0-9]{,2}"
grep -E "[0-9.-]{,2}"
grep -E "[A-Za-z0-9.-]{,2}"
```

##other shells
Bash is the default shell on most Linux/Mac systems, but
there are lots of shells besides Bash.
Some other shells include:

* sh
  * the basic shell
* dash
  * simpler than Bash
* zsh
* csh
  * C language inspired shell
* ksh

##glossary
word|meaning
---|---
terminal|another name for the shell / command line / command prompt.  This is the magic black box where you issue super powerful commands to your system
field|column
record|row
record separator|the character that distinguishes units of analysis from each other, usually newline ( "rows" of data )
field separator|the character that distinguishes attributes of a unit of analysis, usually comma (CSV) or tab (TSV)
"plain" text|text you can read from the terminal ( *not* Word docs! )
character encoding|set of systems used to turn computer stuff (bits) into "plain" text (letters, numbers, etc)
carriage return|pesky character sometimes used as a record separator. Don't use it!
newline|the classic record separator usually associated with the 'Enter' key
ASCII|classic simple character encoding really only good for English
UTF-8|awesome modern character encoding good for everything
utils|commands ("utilities") that do one job and do it well
pipes|tool to send the output of one command to become the input of the next command (written like this: |)
standard streams|the inputs (STDIN), ouptuts (STDOUT), and errors (STDERR) produced and used by commands
directory|folder
