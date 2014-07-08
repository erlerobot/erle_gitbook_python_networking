## Five socket cordinates

If you review previous code, you will notice that we have use:
```python
import socket
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
s.bind(('localhost', 1060))
```
We paid particular attention to
the hostnames and IP addresses that their sockets used. But if you read each program listing from the
beginning, you will see that these are only the last two coordinates of five major decisions that were
made during the construction and deployment of each socket object.
In order, here is the full list of values that had to be chosen, and you will see that there are five in all:

- First, the address family makes the biggest decision: it names what kind of network you want to talk
to, out of the many kinds that a particular machine might support.We will always use the value AF_INET.

- Next after the address family comes the socket type. It chooses the particular kind of communication
technique that you want to use on the network you have chosen. the socket interface designers decided to create more generic names for the broad idea
of a packet-based socket, which goes by the name SOCK_DGRAM, and the broad idea of a reliable flowcontrolled
data stream, which as we have seen is known as a SOCK_STREAM.

- The third field in the socket() call, the protocol, is rarely used because once you have specified the
address family and socket type, you have narrowed down the possible protocols to one major option.

- The fourth and fifth fields are, then, the IP address and UDP or TCP port number that were
explained in detail in the last chapters.

