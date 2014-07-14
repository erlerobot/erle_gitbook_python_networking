## Socket Options

The POSIX socket interface also supports all sorts of socket options that control specific behaviors of
network sockets. These are accessed through the Python socket methods `getsockopt()` and `setsockopt()`,
using the options you will find documented for your operating system.
You can find this options described in the [Python documentation](https://docs.python.org/2/library/socket.html).

When setting socket options,the set call is similar to:
```
value = s.getsockopt(socket.SOL_SOCKET, socket.SO_BROADCAST)
s.setsockopt(socket.SOL_SOCKET, socket.SO_BROADCAST, value)
```
 Here are some of the more common options:

- SO_BROADCAST: Allows broadcast UDP packets to be sent and received; see the next
section for details.


- SO_DONTROUTE: Only be willing to send packets that are addressed to hosts on
subnets to which this computer is connected directly.


- SO_TYPE: When passed to `getsockopt()`, this returns to you regardless of whether a
socket is of type `SOCK_DGRAM `and can be used for UDP, or it is of type `SOCK_STREAM`
and instead supports the semantics of TCP.

NOTE:

If UDP has a superpower, it is its ability to support **broadcast**: instead of sending a packet to some
specific other host, you can point it at an entire subnet to which your machine is attached and have the
physical network card broadcast the packet so that all attached hosts see it without its having to be
copied separately to each one of them.[Here](http://svn.python.org/projects/python/branches/pep-0384/Demo/sockets/broadcast.py) and [here](https://github.com/misheska/foundations-of-python-network-programming/blob/master/python2/02/udp_broadcast.py) you can find two example of broadcasting.
