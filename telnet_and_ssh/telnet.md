## Telnet

Telnet is a network protocol used on the Internet or local area networks to provide a bidirectional interactive text-oriented communication facility using a virtual terminal connection. User data is interspersed in-band with Telnet control information in an 8-bit byte oriented data connection over the Transmission Control Protocol (TCP).

Telnet is
insecure: anyone watching your Telnet packets fly by will see your username, password, and everything
you do on the remote system. It is clunky. And it has been completely abandoned for most systems
administration.

In case you are having to write a Python program that
has to speak Telnet to one of these devices, here are a few pointers on using the Python [telnetlib](https://docs.python.org/2/library/telnetlib.html?highlight=telnetlib#telnetlib).The telnetlib module provides a Telnet class that implements the Telnet protocol.

First, you have to realize that all Telnet does is to establish a channel and to send the things you type, and receive the things the remote system says,
back and forth across that channel. This means that Telnet is ignorant of all sorts of things of which you
might expect a remote-shell protocol to be aware.

For example, it is conventional that when you Telnet to a Unix machine, you are presented with aa
login: prompt at which you type your username, and a password: prompt where you enter your
password.

The fact that Telnet is ignorant about authentication has an important consequence: you cannot
type anything on the command line itself to get yourself pre-authenticated to the remote system, nor
avoid the login and password prompts that will pop up when you first connect! If you are going to use
plain Telnet, you are going to have to somehow watch the incoming text for those two prompts (or
however many the remote system supplies) and issue the correct replies.

Obviously, if systems vary in what username and password prompts they present, then you can
hardly expect standardization in the error messages or responses that get sent back when your password
fails. That is why Telnet is so hard to script and program from a language like Python and a library like
`telnetlib`.

So if you are using Telnet, then you are playing a text game: you watch for text to arrive, and then try
to reply with something intelligible to the remote system. To help you with this, the Python `telnetlib`
provides not only basic methods for sending and receiving data, but also a few routines that will watch
and wait for a particular string to arrive from the remote system.

`telnet_login.py`connects to localhost, which in this case is my Ubuntu laptop, where I have just run
aptitude install telnetd so that a Telnet daemon is now listening on its standard port 23.

```

import telnetlib

t = telnetlib.Telnet('localhost')
# t.set_debuglevel(1)        # uncomment this for debugging messages

t.read_until('login:')
t.write('brandon\n')
t.read_until('assword:')     # let "P" be capitalized or not
t.write('mypass\n')
n, match, previous_text = t.expect([r'Login incorrect', r'\$'], 10)
if n == 0:
    print "Username and password failed - giving up"
else:
    t.write('exec uptime\n')
    print t.read_all()       # keep reading until the connection closes
    ```

If the script is successful, it shows you what the simple uptime command prints on the remote
system:
```
root@erlerobot:~/Python_files# python telnet_login.py
10:24:43 up 5 days, 12:13, 14 users, load average: 1.44, 0.91, 0.73
```

The listing shows you the general structure of a session powered by `telnetlib`. First, a connection is
established, which is represented in Python by an instance of the Telnet object. Here only the hostname
is specified, though you can also provide a port number to connect to some other service port than
standard Telnet.
You can call `set_debuglevel(1)` if you want your Telnet object to print out all of the strings that it
sends and receives during the session. This actually turned out to be important for writing even the very
simple script shown in the listing, because in two different cases it got hung up, and I had to re-run it
with debugging messages turned on so that I could see the actual output and fix the script.  I generally turn off debugging only once a program is working perfectly, and turn it back on
whenever I want to do more work on the script.

Note that Telnet does not disguise the fact that its service is backed by a TCP socket, and will pass
through to your program any `socket.error` and `socket.gaierror` exceptions that are raised.
Once the Telnet session is established, interaction generally falls into a receive-and-send pattern,
where you wait for a prompt or response from the remote end, then send your next piece of information.
The listing illustrates two methods of waiting for text to arrive:

- The very simple `read_until()` method watches for a literal string to arrive, then
returns a string providing all of the text that it received from the moment it started
listing until the moment it finally saw the string you were waiting for.

- The more powerful and sophisticated `expect()` method takes a list of Python
regular expressions. Once the text arriving from the remote end finally adds up to
something that matches one of the regular expressions, `expect() returns three
items: the index in your list of the pattern that matched, the regular expression
SRE_Match object itself, and the text that was received leading up to the matching
text. For more information on what you can do with a SRE_Match, including finding
the values of any sub-expressions in your pattern, read the Standard Library
documentation for the re module.

If the script sees an error message because of an incorrect password—and does not get stuck waiting
forever for a login or password prompt that never arrives or that looks different than it was expecting—
then it exits:
```
root@erlerobot:~/Python_files#  python telnet_login.py
Username and password failed - giving up
```

If you wind up writing a Python script that has to use Telnet, it will simply be a larger or more
complicated version of the same simple pattern shown here.
Both `read_until()` and `expect()` take an optional second argument named timeout that places a
maximum limit on how long the call will watch for the text pattern before giving up and returning
control to your Python script. If they quit and give up because of the timeout, they do not raise an error;
instead—awkwardly enough—they just return the text they have seen so far, and leave it to you to figure
out whether that text contains the pattern.
There are a few odds and ends in the Telnet object that we need not cover here. You will find them
in the telnetlib Standard Library documentation—including an `interact()` method that lets the user
“talk” directly over your Telnet connection using the terminal! This kind of call was very popular back in
the old days, when you wanted to automate login but then take control and issue normal commands
yourself.


Normally, each time a Telnet server sends an option request, telnetlib flatly refuses to send or
receive that option. But you can provide a Telnet object with your own callback function for processing
options; a modest example is shown in `telnet_codes.py`. For most options, it simply re-implements the
default telnetlib behavior and refuses to handle any options (and always remember to respond to each
option one way or another; failing to do so will often hang the Telnet session as the server waits forever
for your reply). But if the server expresses interest in the “terminal type” option, then this client sends
back a reply of “mypython,” which the shell command it runs after logging in then sees as its $TERM
environment variable.

```

from telnetlib import Telnet, IAC, DO, DONT, WILL, WONT, SB, SE, TTYPE

def process_option(tsocket, command, option):
    if command == DO and option == TTYPE:
        tsocket.sendall(IAC + WILL + TTYPE)
        print 'Sending terminal type "mypython"'
        tsocket.sendall(IAC + SB + TTYPE + '\0' + 'mypython' + IAC + SE)
    elif command in (DO, DONT):
        print 'Will not', ord(option)
        tsocket.sendall(IAC + WONT + option)
    elif command in (WILL, WONT):
        print 'Do not', ord(option)
        tsocket.sendall(IAC + DONT + option)

t = Telnet('localhost')
# t.set_debuglevel(1)        # uncomment this for debugging messages

t.set_option_negotiation_callback(process_option)
t.read_until('login:', 5)
t.write('brandon\n')
t.read_until('assword:', 5)  # so P can be capitalized or not
t.write('mypass\n')
n, match, previous_text = t.expect([r'Login incorrect', r'\$'], 10)
if n == 0:
    print "Username and password failed - giving up"
else:
    t.write('exec echo $TERM\n')
    print t.read_all()
    ```


