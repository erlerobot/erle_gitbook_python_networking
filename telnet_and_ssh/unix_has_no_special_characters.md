## Unix Has No Special Characters

Like many very useful statements, the bold claim of the title of this section is, alas, a lie. There is, in fact,
a character that Unix considers special.
But, in general, Unix has no special characters, and this is a very important fact for you to grasp.

On the one hand, it makes it very easy to, say, name
all of the files in the current directory as arguments to a command; but on the other hand, it can be very
difficult to echo a message to the screen that mixes single quotes and double-quotes.

The simple lesson of this section is that the whole set of conventions to which you are accustomed
has nothing to do with your operating system; they are simply and entirely a behavior of the bash shell,
or of whichever of the other popular (or arcane) shells that you are using. It does not matter how familiar
the rules seem, or how difficult it is for you to imagine using a Unix-like system without them. If you take
bash away, they are simply not there.
You can observe this quite simply by taking control of the operating system's process launcher
yourself and trying to throw some special characters at a familiar command:
```python
>>> import subprocess
>>> args = ['echo', 'Sometimes an', '*', 'just means an', '*']
>>> subprocess.call(args)
```
Sometimes an * just means an *
Here, we are bypassing all of the shell applications that are available for interpreting commands,
and we are telling the operating system to start a new process using precisely the list of arguments we
have provided. And the process—the echo command, in this case—is getting exactly those characters,
instead of having the * turned into a list of file names first.
Though we rarely think about it, the most common “special” character is one we use all the time:
the space character. Rather than assume that you actually mean each space character to be passed to the
command you are invoking, the shell instead interprets it as the delimiter separating the actual text you
want the command to see. This causes endless entertainment when people include spaces in Unix file
names, and then try to move the file somewhere else:
```
root@erlerobot:~# mv Smith Contract.txt ~/Documents
mv: cannot stat `Smith': No such file or directory
mv: cannot stat `Contract.txt': No such file or directory
```
To make the shell understand that you are talking about one file with a space in its name, not two
files, you have to contrive something like one of these possible command lines:
```
root@erlerobot:~# mv Smith\ Contract.txt ~/Documents
root@erlerobot:~# mv "Smith Contract.txt" ~/Documents
root@erlerobot:~# mv Smith*Contract.txt ~/Documents
```
That last possibility obviously means something quite different—since it will match any file name
that happens to start with Smith and end with Contract.txt, regardless of whether the text between them
is a simple space character or some much longer sequence of text—but I have seen many people type it
in frustration who are still learning shell conventions and cannot remember how to type a literal space
character for the shell.
If you want to convince yourself that none of the characters that the bash shell has taught you to be
careful about is special, `shell.py`shows a simple shell, written in Python, that treats only the space as
special but passes everything else through literally to the command.
```python
import subprocess

while True:
    args = raw_input('] ').split()
    if not args:
        pass
    elif args == ['exit']:
        break
    elif args[0] == 'show':
        print "Arguments:", args[1:]
    else:
        subprocess.call(args)
        ```
Running this file, result on:
```
root@erlerobot:~#  python shell.py
] echo Hi there!
Hi there!
] echo An asterisk * is not special.
An asterisk * is not special.
] echo The string $HOST is not special, nor are "double quotes".
The string $HOST is not special, nor are "double quotes".
] echo What? No *<>!$ special characters?
What? No *<>!$ special characters?
] show "The 'show' built-in lists its arguments."
Arguments: ['"The', "'show'", 'built-in', 'lists', 'its', 'arguments."']
] exit
```
You can see here absolute evidence that Unix commands—in this case, the /bin/echo command
that we are calling over and over again—do not generally attempt to interpret their arguments as
anything other than strings. The echo command happily accepts double-quotes, dollar signs, and
asterisks, and treats them all as literal characters. As the foregoing show command illustrates, Python is
simply reducing our arguments to a list of strings for the operating system to use in creating a new
process.
What if we fail to split our command into separate arguments?
```python
>>> import subprocess
>>> subprocess.call(['echo hello'])
Traceback (most recent call last):
...
OSError: [Errno 2] No such file or directory
```
The operating system does not know that spaces should be special;
that is a quirk of shell programs, not of Unix-like operating systems themselves! So the system thinks
that it is being asked to run a command literally named echo [space] hello, and, unless you have created
such a file in the current directory, it fails to find it and raises an exception.

To prevent you
from making this mistake, Python stops you in your tracks if you include a null character in a commandline
argument:
```python
>>> import subprocess
>>> subprocess.call(['echo', 'Sentences can end\0 abruptly.'])
Traceback (most recent call last):
...
TypeError: execv() arg 2 must contain only strings
```
Since every command on the system is designed to live within this limitation, you will
generally find there is never any reason to put null characters into command-line arguments anyway.
