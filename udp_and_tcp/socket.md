## Socket (UDP)

As we have seen sockets makes talking to arbitrary machines around the world unbelievably easy (at least compared to other schemes).


When you craft programs that accept port numbers from user input like the command line or
configuration files, it is friendly to allow not just numeric port numbers but to let users type humanreadable
names for well-known ports. These names are standard, and are available through the
`getservbyname()` call supported by Python’s standard socket module. If we want to ask where the
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
root@erlerobot:~/Python_files# python udp_local.py
usage: udp_local.py server|client
```
Noe try to run first the server:
```
root@erlerobot:~/Python_files# python ude_local.py server
Listening at ('127.0.0.1', 1060)
```
And then in a new Terminal window the client:
```
root@erlerobot:~/Python_files# python udp_local.py client
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
Until we send a message with a client, our `recvfrom()` call will wait forever.
