## Socket

Rather than trying to invent its own API for doing networking, Python made an interesting decision:
it simply provides a slightly object-based interface to all of the normal, gritty, low-level operating system
calls that are normally used to accomplish networking tasks on POSIX-compliant operating systems.


So, Python exposes the normal POSIX calls for raw UDP and TCP connections rather than trying to
invent any of its own. And the normal POSIX networking calls operate around a central concept called a
socket.

If you have ever worked with POSIX before, you will probably have run across the fact that instead of
making you repeat a file name over and over again, the calls let you use the file name to create a “file
descriptor” that represents a connection to the file, and through which you can access the file until you
are done working with it.
Sockets provide the same idea for the networking realm: when you ask for access to a line of
communication—like a UDP port, as we are about to see—you create one of these abstract “socket”
objects and then ask for it to be bound to the port you want to use. If the binding is successful, then the socket “holds on to” that port number for.

When you craft programs that accept port numbers from user input like the command line or
configuration files, it is friendly to allow not just numeric port numbers but to let users type humanreadable
names for well-known ports. These names are standard, and are available through the
getservbyname() call supported by Python’s standard socket module. If we want to ask where the
Domain Name Service lives, we could have found out this way:

```python
import socket
socket.getservbyname('domain')
53
```
Now examine the following code which shows a simple server and
client. You can see already that all sorts of operations are taking place that are drawn from the socket
module in the Python Standard Library.

```python

#UDP client and server on localhost
import socket, sys
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
MAX = 65535
PORT = 1060
if sys.argv[1:] == ['server']:
  s.bind(('127.0.0.1', PORT))
  print 'Listening at', s.getsockname()
  while True:
    data, address = s.recvfrom(MAX)
    print 'The client at', address, 'says', repr(data)
    s.sendto('Your data was %d bytes' % len(data), address)
elif sys.argv[1:] == ['client']:
  print 'Address before sending:', s.getsockname()
  s.sendto('This is my message', ('127.0.0.1', PORT))
  print 'Address after sending', s.getsockname()
  data, address = s.recvfrom(MAX) # overly promiscuous - see text!
  print 'The server', address, 'says', repr(data)
else:
   print >>sys.stderr, 'usage: udp_local.py server|client'
```
When runing it, you should get something similar to this:
```
root@erlerobot:~/Python_files# python socket.py
usage: udp_local.py server|client
```
Noe try to run first the server:
```
root@erlerobot:~/Python_files# python socket.py server
Listening at ('127.0.0.1', 1060)
```
And then in a new Terminal window the client:
```
root@erlerobot:~/Python_files# python socket.py client
Address before sending: ('0.0.0.0', 0)
Address after sending ('0.0.0.0', 59726)
The server ('127.0.0.1', 1060) says 'Your data was 18 bytes'
```
In the server window will appear a new line:
```
The client at ('127.0.0.1', 59726) says 'This is my message'
```
Note that the Python program can always use a socket’s `getsockname() `method to retrieve the
current IP and port to which the socket is bound.
Once the socker has been bound successfully, the server is ready to start receiving requests! It enters
a loop and repeatedly runs `recvfrom()`, telling the routine that it will happily receive messages up to a
maximum length of MAX, which is equal to 65535 bytes—a value that happens to be the greatest length
that a UDP packet can possibly have, so that we will always be shown the full content of each packet.
Until we send a message with a client, our `recvfrom() call will wait forever.
