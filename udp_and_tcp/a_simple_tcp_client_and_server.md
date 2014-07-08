## A Simple TCP Client and Server

Here you can find the code  of a simple TCP client and server that send and receive 16 octets:
```python
import socket, sys
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
HOST = sys.argv.pop() if len(sys.argv) == 3 else '127.0.0.1'
PORT = 1060
def recv_all(sock, length):
  data = ''
  while len(data) < length:
    more = sock.recv(length - len(data))
    if not more:
      raise EOFError('socket closed %d bytes into a %d-byte message'
      % (len(data), length))
    data += more
  return data
if sys.argv[1:] == ['server']:
  s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
  s.bind((HOST, PORT))
  s.listen(1)
  while True:
    print 'Listening at', s.getsockname()
    sc, sockname = s.accept()
    print 'We have accepted a connection from', sockname
    print 'Socket connects', sc.getsockname(), 'and', sc.getpeername()
    message = recv_all(sc, 16)
    print 'The incoming sixteen-octet message says', repr(message)
    sc.sendall('Farewell, client')
    sc.close()
    print 'Reply sent, socket closed'
elif sys.argv[1:] == ['client']:
  s.connect((HOST, PORT))
  print 'Client has been assigned socket name', s.getsockname()
  s.sendall('Hi there, server')
  reply = recv_all(s, 16)
  print 'The server said', repr(reply)
  s.close()
else:
  print >>sys.stderr, 'usage: tcp_local.py server|client [host]'
  ```

First, the TCP `connect()` call is not the innocuous bit of local
socket configuration that it is in the case of UDP, where it merely sets a default address used with any
subsequent `send()` calls, and places a filter on packets arriving at our socket. Here, `connect()` is a real live
network operation that kicks off the three-way handshake between the client and server machine so that
they are ready to communicate. This means that `connect()` can fail, as you can verify quite easily by
executing this script when the server is not running:

  ```
  root@erlerobot:~/Python_files# python tcp_sixteen.py client
  Traceback (most recent call last):
  File "tcp_sixteen.py", line 29, in <module>
    s.connect((HOST, PORT))
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/socket.py", line 224, in meth
    return getattr(self._sock,name)(*args)
socket.error: [Errno 61] Connection refused
```
You will see that this TCP client is in one way much simpler than our UDP client, because it
does not need to make any provision for missing data. Because of the assurances that TCP provides, it
can `send()` data without checking whether the remote end receives it, and run `recv()` without having to
consider the possibility of re-transmitting its request.

When we perform a TCP `send()`, our operating system’s networking stack will face one of three
situations:
- The data can be immediately accepted by the system, either because the network
card is immediately free to transmit, or because the system has room to copy the
data to a temporary outgoing buffer so that your program can continue running.
In these cases, `send()` returns immediately, and it will return the length of your
data string because the whole string was transmitted.
- Another possibility is that the network card is busy and that the outgoing data
buffer for this socket is full and the system cannot—or will not—allocate any more
space. In this case, the default behavior of `send()` is simply to block, pausing your
program until the data can be accepted.
- There is a final, hybrid possibility: that the outgoing buffers are almost full, but not
quite, and so part of the data you are trying to send can be immediately queued,
but the rest will have to wait. In this case, `send()` completes immediately and
returns the number of bytes accepted from the beginning of your data string, but
leaves the rest of the data unprocessed.

Fortunately, Python does not force us to do this dance ourselves every time we have a block of data
to send: the Standard Library socket implementation provides a friendly `sendall()` method. Not only is `sendall() `faster than doing it ourselves, it releases the Global Interpreter Lock during its
loop so that other Python threads can run without contention until all of the data has been transmitted.
Unfortunately, no equivalent is provided for the `recv() call`, despite the fact that it might return only
part of the data that is on the way from the client. Internally, the operating system implementation of
`recv()` uses logic very close to that used when sending:
- If no data is available, then `recv()` blocks and your program pauses until data
arrives.
- If plenty of data is available already in the incoming buffer, then you are given as
many bytes as you asked `recv()` for.
- But if the buffer contains a bit of data, but not as much as you are asking for, then
you are immediately returned what does happen to be there, even if it is not as
much as you have asked for.


In the code stored in `tcp_sixteen.py`, you can see how the distinction between active and listening socket is carried through in actual server code. The link,
which might strike you as odd at first, is that a listening socket actually produces new connected sockets
as the return value that you get by listening. Follow the steps in the program listing to see the order in
which the socket operations occur.

Run the server:
```
root@erlerobot:~/Python_files#  python tcp_sixteen.py server
Listening at ('127.0.0.1', 1060)
```
And then the client(in another terminal window):
```
root@erlerobot:~/Python_files# python tcp_sixteen.py client
Client has been assigned socket name ('127.0.0.1', 49607)
The server said 'Farewell, client'
```

The server returns this:

```
We have accepted a connection from ('127.0.0.1', 49607)
Socket connects ('127.0.0.1', 1060) and ('127.0.0.1', 49607)
The incoming sixteen-octet message says 'Hi there, server'
Reply sent, socket closed
Listening at ('127.0.0.1', 1060)
```
