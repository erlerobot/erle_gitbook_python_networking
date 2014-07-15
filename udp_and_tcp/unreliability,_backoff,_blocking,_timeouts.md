## Unreliability, Backoff, Blocking, Timeouts

Because the client and server in the previous section were both running on the same machine and
talking through its loopback interface—which is not even a physical network card that could experience
a signaling glitch and lose a packet, but merely a virtual connection back to the same machine deep in
the network stack—there was no real way that packets could get lost, and so we did not actually see any
of the inconvenience of UDP.

You can run this client and server example on two
different machines on the Internet. And instead of always answering client requests, this server
randomly chooses to answer only half of the requests coming in from clients—which will let us
demonstrate how to build reliability into our client code, without waiting what might be hours for a real
dropped packet to occur.

```python
import random, socket, sys
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
MAX = 65535
PORT = 1060
if 2 <= len(sys.argv) <= 3 and sys.argv[1] == 'server':
  interface = sys.argv[2] if len(sys.argv) > 2 else ''
  s.bind((interface, PORT))
  print 'Listening at', s.getsockname()
  while True:
    data, address = s.recvfrom(MAX)
    if random.randint(0, 1):
      print 'The client at', address, 'says:', repr(data)
      s.sendto('Your data was %d bytes' % len(data), address)
    else:
      print 'Pretending to drop packet from', address
elif len(sys.argv) == 3 and sys.argv[1] == 'client':
  hostname = sys.argv[2]
  s.connect((hostname, PORT))
  print 'Client socket name is', s.getsockname()
  delay = 0.1
  while True:
    s.send('This is another message')
    print 'Waiting up to', delay, 'seconds for a reply'
    s.settimeout(delay)
    try:
      data = s.recv(MAX)
    except socket.timeout:
      delay *= 2 # wait even longer for the next request
      if delay > 2.0:
        raise RuntimeError('I think the server is down')
    except:
      raise # a real error, so we let the user see it
    else:
      break # we are done, and can stop looping
  print 'The server says', repr(data)
else:
  print >>sys.stderr, 'usage: udp_remote.py server [ <interface> ]'
  print >>sys.stderr, ' or: udp_remote.py client <host>'
  sys.exit(2)
  ```
  Running the file itself result on:
  ```
 root@erlerobot:~/Python_files# python socket1.py
 usage: udp_remote.py server [ <interface> ]
 or: udp_remote.py client <host>
 ```
 Then run the server:
```
root@erlerobot:~/Python_files# python udp_remote.py server
Listening at ('0.0.0.0', 1060)
```
And now the client, remember to pass the hostname where the server script is being run(in this case the same machine):
```
root@erlerobot:~/Python_files# python udep_remote.py client 127.0.0.1
Client socket name is ('127.0.0.1', 54770)
Waiting up to 0.1 seconds for a reply
Waiting up to 0.2 seconds for a reply
Waiting up to 0.4 seconds for a reply
Waiting up to 0.8 seconds for a reply
The server says 'Your data was 23 bytes'
```


  As you can see, each time a request is received, the server uses `randint()` to flip a coin to decide
whether this request will be answered, so that we do not have to keep running the client all day waiting
for a real dropped packet.
The cliente will find that one or more of its requests never result in replies.
