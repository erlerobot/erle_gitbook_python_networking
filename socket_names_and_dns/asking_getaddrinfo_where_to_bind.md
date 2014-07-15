## Asking getaddrinfo() Where to Bind

Before tackling all of the options that getaddrinfo() supports, it will be more useful to see how it is used
to support three basic network operations. We will tackle them in the order that you might perform
operations on a socket: binding, connecting, and then identifying a remote host who has sent you
information.
```python
>>> import socket
>>> from socket import getaddrinfo
>>> getaddrinfo(None, 'smtp', 0, socket.SOCK_STREAM, 0, socket.AI_PASSIVE)
[(2, 1, 6, '', ('0.0.0.0', 25)), (30, 1, 6, '', ('::', 25, 0, 0))]
>>> getaddrinfo(None, 53, 0, socket.SOCK_DGRAM, 0, socket.AI_PASSIVE)
[(2, 2, 17, '', ('0.0.0.0', 53)), (30, 2, 17, '', ('::', 53, 0, 0))]
>>>
```
Here we asked about where we should `bind()` a socket if we want to serve SMTP traffic using TCP,
and if we want to serve DNS traffic using DCP, respectively. The answers we got back in each case are the
appropriate wildcard addresses that will let us bind to every IPv4 and every IPv6 interface on the local
machine with all of the right values for the socket family, socket type, and protocol in each case.
If you instead want to *`bind()` to a particular IP address that you know that the local machine holds*,
then omit the AI_PASSIVE flag and just specify the hostname. For example, here are two ways that you
might try binding to localhost:
```python
>>> getaddrinfo('127.0.0.1', 'smtp', 0, socket.SOCK_STREAM, 0)
[(2, 1, 6, '', ('127.0.0.1', 25))]
>>> getaddrinfo('localhost', 'smtp', 0, socket.SOCK_STREAM, 0)
[(30, 1, 6, '', ('::1', 25, 0, 0)), (2, 1, 6, '', ('127.0.0.1', 25)), (30, 1, 6, '', ('fe80::1%lo0', 25, 0, 1))]
>>>

```
You can see that supplying the IPv4 address for the localhost locks you down to receiving
connections only over IPv4, while using the symbolic name localhost (at least on a Linux laptop, with
a well-configured /etc/hosts file) makes available both the IPv4 and IPv6 local names for the machine.
