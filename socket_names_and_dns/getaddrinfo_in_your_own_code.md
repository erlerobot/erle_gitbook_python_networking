## getaddrinfo() in your own code
Here you have a quick example of how `getaddrinfo()` looks in actual
code in `www_ping.py`.
```python

import socket, sys

if len(sys.argv) != 2:
    print >>sys.stderr, 'usage: www_ping.py <hostname_or_ip>'
    sys.exit(2)

hostname_or_ip = sys.argv[1]

try:
    infolist = socket.getaddrinfo(
        hostname_or_ip, 'www', 0, socket.SOCK_STREAM, 0,
        socket.AI_ADDRCONFIG | socket.AI_V4MAPPED | socket.AI_CANONNAME,
        )
except socket.gaierror, e:
    print 'Name service failure:', e.args[1]
    sys.exit(1)

info = infolist[0]  # per standard recommendation, try the first one
socket_args = info[0:3]
address = info[4]
s = socket.socket(*socket_args)
try:
    s.connect(address)
except socket.error, e:
    print 'Network failure:', e.args[1]
else:
    print 'Success: host', info[3], 'is listening on port 80'
    ```
    It performs a simple are-you-there test of whatever web server you name on the command line by
attempting a quick connection to port 80 with a streaming socket. Using the script would look
something like this:

```
root@erlerobot:~/Python_files#
root@erlerobot:~/Python_files# python www_ping.py mit.edu
Success: host mit.edu is listening on port 80
root@erlerobot:~/Python_files# python www_ping.py smtp.google.com
Name service failure: nodename nor servname provided, or not known
root@erlerobot:~/Python_files# www_ping.py no-such-host.com
Name service failure: nodename nor servname provided, or not known
root@erlerobot:~/Python_files#
```



Note that the `socket() `constructor does not take a list of three items as its parameter. Instead,
the parameter list is introduced by an asterisk, which means that the three elements of the socket_args
list are passed as three separate parameters to the constructor.
