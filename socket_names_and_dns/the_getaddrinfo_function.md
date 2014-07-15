## The getaddrinfo() function

To make your code simple, powerful, and immune from the complexities of the transition from IPv4 to
IPv6, you should turn your attention to one of the most powerful tools in the Python socket user's
arsenal: `getaddrinfo()`.
The `getaddrinfo()` function sits in the socket module along with most other operations that involve
addresses (rather than being a socket method). Unless you are doing something specialized, it is
probably the only routine that you will ever need to transform the hostnames and port numbers that
your users specify into addresses that can be used by socket methods.
Its approach is simple: rather than making you attack the addressing problem piecemeal, which is
necessary when using the older routines in the socket module, it lets you specify everything you know
about the connection that you need to make in a single call. In response, it returns all of the coordinates
we discussed earlier that are necessary for you to create and connect a socket to the named destination.

If we visit [Python Official Documentation](https://docs.python.org/2/library/socket.html) we find this some interesting eplanations. First the syntaxis is the following:
```
socket.getaddrinfo(host, port[, family[, socktype[, proto[, flags]]]])
```
So what  `getaddrinfo()`does is;
translate the host/port argument into a sequence of 5-tuples that contain all the necessary arguments for creating a socket connected to that service. host is a domain name, a string representation of an IPv4/v6 address or None. port is a string service name such as 'http', a numeric port number or None. By passing None as the value of host and port, you can pass NULL to the underlying C API.

The function returns a list of 5-tuples with the following structure:

(family, socktype, proto, canonname, sockaddr)

In these tuples, family, socktype, proto are all integers and are meant to be passed to the `socket()` function. "canonname" will be a string representing the canonical name of the host if AI_CANONNAME is part of the flags argument; else canonname will be empty. "sockaddr" is a tuple describing a socket address, whose format depends on the returned family (a (address, port) 2-tuple for AF_INET, a (address, port, flow info, scope id) 4-tuple for AF_INET6), and is meant to be passed to the socket.connect() method.

Here you find a exapmle of use:

```python
>>> import socket
>>> from pprint import pprint
>>> infolist = socket.getaddrinfo('gatech.edu', 'www')
>>> pprint(infolist)
[(2, 2, 17, '', ('130.207.160.173', 80)),
 (2, 1, 6, '', ('130.207.160.173', 80))]
>>>
>>> ftpca = infolist[0]
>>> ftpca[0:3]
(2, 2, 17)
>>> s = socket.socket(*ftpca[0:3])
>>> ftpca[4]
('130.207.160.173', 80)
>>> s.connect(ftpca[4])
>>>
```

`ftpca` here is an acronym for the order of the variables
that are returned: “family, type, protocol, canonical name, and address,” which contain everything you
need to make a connection.
Here, we have asked about the possible methods for connecting to the HTTP
port of the host gatech.edu, and have been told that there are two ways to do it: by creating a
`SOCK_STREAM` socket (socket type 1) that uses `IPPROTO_TCP` (protocol number 6) or else by using a
`SOCK_DGRAM` (socket type 2) socket with `IPPROTO_UDP (which is the protocol represented by the integer17).


As you can see from the foregoing code snippet, `getaddrinfo()` generally allows not only the
hostname but also the port name to be a symbol rather than an integer.
