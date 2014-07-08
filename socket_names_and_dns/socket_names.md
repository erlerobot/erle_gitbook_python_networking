## Socket names

The last chapter has already introduced you to the fact that sockets cannot be named with a single
primitive Python value like a number or string. Instead, both TCP and UDP use integer port numbers to
share a single machine's IP address among the many different applications that might be running there,
and so the address and port number have to be combined in order to produce a socket name, like this:
```
('18.9.22.69', 80)
```
You will recall that socket names are important at several points in the creation and use of sockets.
For your reference, here are all of the major socket methods that demand of you some sort of socket
name as an argument:

- `mysocket.accept()`: Each time this is called on a listening TCP stream socket that
has incoming connections ready to hand off to the application, it returns a tuple(ordered set of values)
whose second item is the remote address that has connected (the first item in the
tuple is the net socket connected to that remote address).
- `mysocket.bind(address)`: Assigns the socket the local address so that outgoing
packets have an address from which to originate, and so that any incoming
connections from other machines have a name that they can use to connect.
- ` mysocket.connect(address)`: Establishes that data sent through this socket will be
directed to the given remote address. For UDP sockets, this simply sets the default
address used if the caller uses `send()` rather than `sendto()`; for TCP sockets, this
actually negotiates a new stream with another machine using a three-way
handshake, and raises an exception if the negotiation fails.
- `mysocket.getpeername()`: Returns the remote address to which this socket is
connected.
- `mysocket.getsockname()`: Returns the address of this socket's own local endpoint.
- `mysocket.recvfrom(...)`: For UDP sockets, this returns a tuple that pairs a string
of returned data with the address from which it was just sent.
- `mysocket.sendto(data, address)`: An unconnected UDP port uses this method to
fire off a data packet at a particular remote address.

In general, any of the foregoing
methods can receive or return any of the sorts of addresses that follow, meaning that they will work regardless of whether you are using IPv4, IPv6 or others.
