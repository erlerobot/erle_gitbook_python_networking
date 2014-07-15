## Things Are Different in a Terminal

You will probably talk to more programs than just the shell over your Python-powered remote-shell
connection, of course. You will often want to watch the incoming data stream for the information and
errors printed out by the commands you are running. And sometimes you will even want to send data
back, either to provide the remote programs with input, or to respond to questions and prompts that
they present.

When performing tasks like this, you might be surprised to find that programs hang indefinitely
without ever finishing the output that you are waiting on, or that data you send seems to not be getting
through. To help you through situations like this, a brief discussion of Unix terminals is in order.

A terminal typically names a device into which a user types text, and on whose screen the
computer's response can be displayed. If a Unix machine has physical serial ports that could possibly
host a physical terminal, then the device directory will contain entries like /dev/ttyS1 with which
programs can send and receive strings to that device. But most terminals these days are, in reality, other
programs: an xterm terminal, or a Gnome or KDE terminal program, or a PuTTY client on a Windows
machine that has connected via a remote-shell protocol of the kind we will discuss.

But the programs running inside the terminal on your laptop or desktop machine still need to know
that they are talking to a person—they still need to feel like they are talking through the mechanism of a
terminal device connected to a display. So the Unix operating system provides a set of “pseudoterminal”
devices (which might have less confusingly been named “virtual” terminals) with names like
/dev/tty42. When someone brings up an xterm or connects through SSH, the xterm or SSH daemon
grabs a fresh pseudo-terminal, configures it, and runs the user's shell behind it. The shell examines its
standard input, sees that it is a terminal, and presents a prompt since it believes itself to be talking to a
person.

This is a crucial distinction to understand: the shell presents a prompt because, and only because, it
thinks it is connected to a terminal! If you start up a shell and give it a standard input that is not a
terminal—like, say, a pipe from another command—then no prompt will be printed, yet it will still
respond to commands:
```
root@erlerobot:~# cat | bash
echo Here we are inside of bash, with no prompt!
Here we are inside of bash, with no prompt!
python
print 'Python has not printed a prompt, either.'
import sys
print 'Is this a terminal?', sys.stdin.isatty()
```

You can see that Python, also, does not print its usual startup banner, nor does it present any
prompts.

There are even changes in how some commands format their output depending on whether they
are talking to a terminal. Some commands with long lines of output—the ps command comes to mind—
will truncate their lines to your terminal width if used interactively, but produce arbitrarily wide output if
connected to a pipe or file. And, entertainingly enough, the familiar column-based output of the ls
command gets turned off and replaced with a file name on each line (which is, you must admit, an easier
format for reading by another program) if its output is a pipe or file:
```
root@erlerobot:~# ls
Hello.txt         Python_files  hola.txt  virtualenv-1.11.6
Python-3.4.1      build         otro      virtualenv-1.11.6.tar.gz
Python-3.4.1.tgz  gmapenv       text.txt
root@erlerobot:~# ls|cat
Hello.txt
Python-3.4.1
Python-3.4.1.tgz
Python_files
build
gmapenv
hola.txt
otro
text.txt
virtualenv-1.11.6
virtualenv-1.11.6.tar.gz
root@erlerobot:~#
```
A program running behind Telnet, for example, always thinks it is talking to a terminal; so your
scripts or programs must always expect to see a prompt each time the shell is ready for input, and so
forth. But when you make a connection over the more sophisticated SSH protocol, you will actually have
your choice of whether the program thinks that its input is a terminal or just a plain pipe or file. You can
test this easily from the command line if there is another computer you can connect to:
```
root@erlerobot:~# ssh -t asaph
asaph$ echo "Here we are, at a prompt."
Here we are, at a prompt.
```
So when you spawn a command through a modern protocol like SSH, you need to consider whether
you want the program on the remote end thinking that you are a person typing at it through a terminal,
or whether it had best think it is talking to raw data coming in through a file or pipe.

Programs are not actually required to act any differently when talking to a terminal; it is just for our
convenience that they vary their behavior:

- Programs that are often used interactively will present a human-readable prompt
when they are talking to a terminal. But when they think input is coming from a
file, they avoid printing a prompt.


- Sophisticated interactive programs, these days, usually turn on command-line
editing when their input is a TTY.


- Many programs read only one line of input at a time when listening to a terminal,
because humans like to get an immediate response to every command they type.
But when reading from a pipe or file, these same programs will wait until
thousands of characters have arrived before they try to interpret their first batch of
input.

- It is even more common for programs to adjust their output based on whether
they are talking to a terminal.

Both of the last two issues, which involve buffering, cause all sorts of problems when you take a
process that you usually do manually and try to automate it—because in doing so you often move from
terminal input to input provided through a file or pipe, and suddenly you find that the programs behave
quite differently, and might even seem to be hanging because “print” statements are not producing
immediate output, but are instead saving up their results to push out all at once when their output
buffer is full.

You can see this easily with a simple Python program (since Python is one of the applications that
decides whether to buffer its output based on whether it is talking to a terminal) that prints a message,
waits for a line of input, and then prints again:
```
root@erlerobot:~#  python -c 'print "talk:"; s = raw_input(); print "you said", s'
talk:
hi
you said hi
root@erlerobot:~#  python -c 'print "talk:"; s = raw_input(); print "you said", s' | cat
hi
talk:
you said hi
```
You can see that in the first instance, when Python knew its output was a terminal, it printed talk:
immediately. But in the second instance, its output was a pipe to the cat command, and so it decided
that it could save up the results of that first print statement and batch them together with the rest of the
program's output, so that both lines of output appeared only once you had provided your input and the
program was ending.

The foregoing problem is why many carefully written programs, both in Python and in other
languages, frequently call `flush()` on their output to make sure that anything waiting in a buffer goes
ahead and gets sent out, regardless of whether the output looks like a terminal.
So those are the basic problems with terminals and buffering: programs change their behavior,
often in idiosyncratic ways, when talking to a terminal (think again of the ls example), and they often
start heavily buffering their output if they think they are writing to a file or pipe.

