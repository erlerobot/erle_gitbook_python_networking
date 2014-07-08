## Asking getaddrinfo() About Services

The majority uses of `getaddrinfo()` are outward-looking,
and generate information suitable for connecting you to other applications. In all such cases, you can
either use an empty string to indicate that you want to connect back to the localhost using the loopback
interface, or provide a string giving an IPv4 address, IPv6 address, or hostname to name your
destination.
The usual use of `getaddrinfo()` in all other cases—which, basically, is when you are preparing to
`connect()` or ``sendto()`—is to specify the AI_ADDRCONFIG flag, which filters out any addresses that are impossible for your computer to reach. For example, an organization might have both an IPv4 and an
IPv6 range of IP addresses; but if your particular host supports only IPv4, then you will want the results
filtered to include only addresses in that family. In case the local machine has only an IPv6 network
interface but the service you are connecting to is supporting only IPv4, the AI_V4MAPPED will return you
those IPv4 addresses re-encoded as IPv6 addresses that you can actually use.
So you will usually use `getaddrinfo()` this way when connecting:
```python
>>> getaddrinfo('ftp.kernel.org', 'ftp', 0, socket.SOCK_STREAM, 0, socket.AI_ADDRCONFIG | socket.AI_V4MAPPED)
[(2, 1, 6, '', ('199.204.44.194', 21)), (2, 1, 6, '', ('198.145.20.140', 21)), (2, 1, 6, '', ('149.20.4.69', 21))]
>>>
```
And we have gotten exactly what we wanted: every way to connect to a host named ftp.kernel.org
through a TCP connection to its FTP port.

Here is another query, which describes how I can connect from my laptop to the HTTP interface of
the IANA that assigns port numbers in the first place:
```python
>>> getaddrinfo('iana.org', 'www', 0, socket.SOCK_STREAM, 0,socket.AI_ADDRCONFIG | socket.AI_V4MAPPED)
[(2, 1, 6, '', ('192.0.43.8', 80))]
>>>
```
If we take away our
carefully chosen flags in the sixth parameter, then we will also be able to see their IPv6 address:
```
>>> getaddrinfo('iana.org', 'www', 0, socket.SOCK_STREAM, 0)
[(2, 1, 6, '', ('192.0.43.8', 80)), (30, 1, 6, '', ('2001:500:88:200::8', 80, 0, 0))]
>>>

```
