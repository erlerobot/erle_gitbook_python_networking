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

-The fourth and fifth fields are, then, the IP address and UDP or TCP port number that were
explained in detail in the last chapters.

#####IPv6

And having explained all of that, it turns out that this book actually does need to introduce one
additional address family, beyond the AF_INET we have used so far: the address family for IPv6, named
AF_INET6, which is the way forward into a future where the world does not, in fact, run out of IP
addresses.

In Python you can test directly for whether the underlying platform supports IPv6 by checking the
`has_ipv6` Boolean attribute inside the socket module:
```python
>>> import socket
>>> socket.has_ipv6
True
```
But note that this does not tell you whether an actual IPv6 interface is up and configured and can
currently be used to send packets anywhere; it is purely an assertion about whether IPv6 support has
been compiled into the operating system, not about whether it is in use.

The differences that IPv6 will make for your Python code might sound quite daunting, if listed one
right after the other:
- Your sockets have to be prepared to have the family AF_INET6 if you are called
upon to operate on an IPv6 network.
- No longer do socket names consist of just two pieces, an address and a port
number; instead, they can also involve additional coordinates that provide “flow”
information and a “scope” identifier.
- The pretty IPv4 octets like 18.9.22.69 that you might already be reading from
configuration files or from your command-line arguments will now sometimes be
replaced by IPv6 host addresses instead, which you might not even have good
regular expressions for yet. They have lots of colons, they can involve hexadecimal
numbers, and in general they look quite ugly.
