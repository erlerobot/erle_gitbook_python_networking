## Asking getaddrinfo() for Pretty Hostnames

One last circumstance that you will commonly encounter is where you either are making a new
connection, or maybe have just received a connection to one of your own sockets, and you want an
attractive hostname to display to the user or record in a log file. This is slightly dangerous because a
hostname lookup can take quite a bit of time, even on the modern Internet, and might return a
hostname that no longer works by the time you go and check your logs—so for log files, try to record
both the hostname and raw IP address.
But if you have a good use for the “canonical name” of a host, then try running `getaddrinfo()` with
the AI_CANONNAME flag turned on, and the fourth item of any of the tuples that it returns—that were
always empty strings in the foregoing examples, you will note—will contain the canonical name:
```
>>> import socket
>>> from socket import getaddrinfo
>>> getaddrinfo('iana.org', 'www', 0, socket.SOCK_STREAM, 0,socket.AI_ADDRCONFIG | socket.AI_V4MAPPED | socket.AI_CANONNAME)
[(2, 1, 6, 'iana.org', ('192.0.43.8', 80))]
>>>

```

