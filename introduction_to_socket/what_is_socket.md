## What is socket?


Rather than trying to invent its own API for doing networking, Python made an interesting decision:
it simply provides a slightly object-based interface to all of the normal, gritty, low-level operating system
calls that are normally used to accomplish networking tasks on POSIX-compliant operating systems.


So, Python exposes the normal POSIX calls for raw UDP and TCP connections rather than trying to
invent any of its own. And the normal POSIX networking calls operate around a central concept called a
socket.

That means that communication between different entities on a network is based on the classic concept Python sockets. Sockets are an abstract concept that designates the end point of a connection. The programs use sockets to communicate with other programs, which may be located on different computers. A socket is defined by the IP address of the machine, the port on which it listens, and the protocol used.

Moreover, if you have ever worked with POSIX before, you will probably have run across the fact that instead of
making you repeat a file name over and over again, the calls let you use the file name to create a “file
descriptor” that represents a connection to the file, and through which you can access the file until you
are done working with it.
Sockets provide the same idea for the networking realm: when you ask for access to a line of
communication—like a UDP port, as we are about to see—you create one of these abstract “socket”
objects and then ask for it to be bound to the port you want to use. If the binding is successful, then the socket “holds on to” that port number for.


You should, as well, be aware of that part of the trouble with understanding these things is that “socket” can mean a number of subtly different things, depending on context. So first, let’s make a distinction between a “client” socket - an endpoint of a conversation, and a “server” socket, which is more like a switchboard operator. The client application (your browser, for example) uses “client” sockets exclusively; the web server it’s talking to uses both “server” sockets and “client” sockets.

From [Python documentation](https://docs.python.org/2/library/socket.html?highlight=socket#socket) we can extract more info about socket module.

