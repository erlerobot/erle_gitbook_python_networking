## Binding to Interfaces(TCP)

the IP address that you pair with a port number
when you perform a bind() operation tells the operating system which network interfaces you are
willing to receive connections from. The example invocations of `tcp_sixteen.py` used the localhost IP address
127.0.0.1, which protects your code from connections originating on other machines.
You can verify this by running `tcp_sixteen.py` in server mode as shown previously, and trying to connect
with a client from another machine:
```
root@erlerobot:~/Python_files# python tcp_sixteen.py client 192.168.1.35
Traceback (most recent call last):
  File "tcp_sixteen.py", line 29, in <module>
    s.connect((HOST, PORT))
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/socket.py", line 224, in meth
    return getattr(self._sock,name)(*args)
socket.error: [Errno 61] Connection refused

```
But if you run the server with an empty string for the hostname, which tells the Python bind()
routine that you are willing to accept connections through any of your machine’s active network
interfaces, then the client can connect successfully from another host:
```
root@erlerobot:~/Python_files# python tcp_sixteen.py server ""
Listening at ('0.0.0.0', 1060)
````
Run the client:
````
root@erlerobot:~/Python_files# python tcp_sixteen.py client 192.168.1.35
Client has been assigned socket name ('192.168.1.35', 49696)
The server said 'Farewell, client'
```
This appear into the server terminal:
```
We have accepted a connection from ('192.168.1.35', 49696)
Socket connects ('192.168.1.35', 1060) and ('192.168.1.35', 49696)
The incoming sixteen-octet message says 'Hi there, server'
Reply sent, socket closed
Listening at ('0.0.0.0', 1060)
``


