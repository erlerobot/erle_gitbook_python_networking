## Terminals Do Buffering

Beyond the program-specific behaviors just described, there are additional problems raised by
terminals.

For example, what happens when you want a program to be reading your input one character at a
time, but the Unix terminal device itself is buffering your keystrokes to deliver them as a whole line? This
common problem happens because the Unix terminal defaults to “canonical” input processing, where it
lets the user enter a whole line, and even edit it by backspacing and re-typing, before finally pressing
“Enter” and letting the program see what he or she has typed.
If you want to turn off canonical processing so that a program can see every individual character as
it is typed, you can use the stty “Set TTY settings” command to disable it:
```
root@erlerobot:~#  stty -icanon
```
Another problem is that Unix terminals traditionally supported a pair of keystrokes for pausing the
output stream so that the user could read something on the screen before it scrolled off and was replaced by more text. Often these were the characters Ctrl+S for “Stop” and Ctrl+Q for “Keep going,”
and it was a source of great annoyance that if binary data worked its way into an automated Telnet
connection that the first Ctrl+S that happened to pass across the channel would pause the terminal and
probably ruin the session.
Again, this setting can be turned off with stty:
```
root@erlerobot:~#  stty -ixon -ixoff
```
 There are
plenty of less famous settings that can also cause you grief. Because there are so many—and because
they vary between Unix implementations—the stty command actually supports two modes, cooked and
raw, that turn dozens of settings like icanon and ixon on and off together:
```
root@erlerobot:~#  stty raw
root@erlerobot:~#  stty cooked
```
In case you make your terminal settings a hopeless mess after some experimentation, most Unix
systems provide a command for resetting the terminal back to reasonable, sane settings ( you might need to hit Ctrl+J to submit the reset command, since
your Return key, whose equivalent is Ctrl+M, actually only functions to submit commands because of a
terminal setting called icrnl):
```
root@erlerobot:~# reset
```
If, instead of trying to get the terminal to behave across a Telnet or SSH session, you happen to be
talking to a terminal from Python, check out the [termios module](https://docs.python.org/2/library/termios.html?highlight=termios#termios) that comes with the Standard Library.This module provides an interface to the POSIX calls for tty I/O control. For a complete description of these calls, see the POSIX or Unix manual pages.
