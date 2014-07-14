## Network Exceptions

Depending on the protocol implementation that you are using, you might have to deal only with
exceptions specific to that protocol, or you might have to deal with both protocol-specific exceptions
and with raw socket errors as well.

The exceptions that are specific to
socket operations are:

- socket.gaierror: This exception is raised when `getaddrinfo()` cannot find a name
or service that you ask about—hence the letters G, A, and I in its name.
```python
>>> import socket
>>> s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
>>> s.connect(('nonexistent.hostname.foo.bar', 80))
Traceback (most recent call last):
...
gaierror: [Errno -5] No address associated with hostname
```



- socket.error: This is the workhorse of the socket module, and will be raised for
nearly every failure that can happen at any stage in a network transmission.



- socket.timeout: This exception is raised only if you, or a library that you are using,
decides to set a timeout on a socket rather than wait forever for a `send()` or `recv()`
to complete. It indicates that the timeout was reached before the operation could
complete normally.
