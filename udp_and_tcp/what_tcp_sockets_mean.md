## What TCP Sockets Mean

As we have mentioned before, TCP uses port numbers to distinguish different applications
running at the same IP address, and follows exactly the same conventions regarding well-known and
ephemeral port number.
With a stateful stream protocol like TCP, the `connect() call becomes the fundamental act upon
which all other network communication hinges. TCP connect() can fail: The remote host might not answer; it might refuse the
connection; or more obscure protocol errors might occur like the immediate receipt of a RST (“reset”)
packet. Because a stream connection involves setting up a persistent connection between two hosts, the
other host needs to be listening and ready to accept your connection.

On the “server side”—which, for the purpose of this chapter, is the conversation partner not doing
the connect() call but receiving the SYN packet that it initiates—an incoming connection generates an
even more momentous event, the creation of a new socket. This is because the standard POSIX interface
to TCP actually involves two completely different kinds of sockets: “passive” listening sockets and active “connected” ones:

- A *passive socket* holds the “socket name”—the address and port number—at
which the server is ready to receive connections. No data can ever be received or
sent by this kind of port; it does not represent any actual network conversation.
Instead, it is how the server alerts the operating system to its willingness to receive
incoming connections in the first place.


- An *active socket* (connected socket), is bound to one particular remote conversation
partner, who has their own IP address and port number. It can be used only for
talking back and forth with that partner, and can be read and written to without
worrying about how the resulting data will be split up into packets—in many
cases, a connected socket can be passed to another POSIX program that expects to
read from a normal file, and the program will never even know that it is talking to
the network.

Note that while a passive socket is made unique by the interface address and port number at which
it is listening (so that no one else is allowed to grab that same address and port), there can be many
active sockets that all share the same local socket name.

What makes an active socket unique is, rather, the four-part coordinate:
*(local_ip, local_port, remote_ip, remote_port)*.
It is this four-tuple by which the operating system names each active TCP connection, and
incoming TCP packets are examined to see whether their source and destination address associate them
with any of the currently active sockets on the system.
