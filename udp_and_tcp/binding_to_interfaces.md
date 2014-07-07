##  Binding to Interfaces

So far we have seen two possibilities for the IP address used in the bind() call that the server makes: you
can use '127.0.0.1' to indicate that you only want packets from other programs running on the same
machine, or use an empty string '' as a wildcard, indicating that you are willing to receive packets from
any interface.
It actually turns out that there is a third choice: you can provide the IP address of one of the
machine’s external IP interfaces, like its Ethernet connection or wireless card, and the server will listen
only for packets destined for those IPs.
First, what if we bind solely to an external interface? Run the server like this, using whatever your
operating system tells you is the external IP address of your system:
```
root@erlerobot:~/Python_files#  python socket1.py server 192.168.1.35
Listening at ('192.168.1.35', 1060)
```
Connecting to this IP address from another machine should still work just fine:
```
root@erlerobot:~/Python_files# python nn.py client 192.168.1.35
Client socket name is ('192.168.1.35', 58824)
Waiting up to 0.1 seconds for a reply
The server says 'Your data was 23 bytes'
```
But if you try connecting to the service through the loopback interface by running the client script
on the same machine, the packets will never be delivered:
```
root@erlerobot:~/Python_files# python socket1.py client 127.0.0.1
Client socket name is ('127.0.0.1', 60251)
Waiting up to 0.1 seconds for a reply
Traceback (most recent call last):
...
socket.error: [Errno 111] Connection refused
```
If you run client again on the same machine, but this
time use the external IP address of the box, even though the client and server are both running there, this will not give any error.So binding to an IP interface might limit which external hosts can talk to you; but it will certainly not
limit conversations with other clients on the same machine, so long as they know the IP address that
they should use to connect.

Now, stop all of the scripts that are
running, and we can try running two servers on the same box.
```
root@erlerobot:~/Python_files# python socket1.py server 127.0.0.1
Listening at ('127.0.0.1', 1060)
```
And then we try running a second one, connected to the wildcard IP address that allows requests
from any address:
```
root@erlerobot:~/Python_files# python socket1.py server
Traceback (most recent call last):
...
socket.error: [Errno 98] Address already in use
```
We have learned something about operating system IP stacks and the
rules that they follow: they do not allow two different sockets to listen at the same IP address and port
number, because then the operating system would not know where to deliver incoming packets.
But what if instead of trying to run the second server against all IP interfaces, we just ran it against
an external IP interface—one that the first copy of the server is not listening to? Let us try:

```
root@erlerobot:~/Python_files# python socket1.py server 192.168.1.35
Listening at ('192.168.1.35', 1060)
```

It worked, this menas that there are now two servers running on this machine, one of which is bound to the inwardlooking
port 1060 on the loopback interface, and the other looking outward for packets arriving on port
1060 from the network to which my wireless card has connected.

IP network stack never thinks of a UDP port as a lone entity that is
either entirely available, or else in use, at any given moment. Instead, it thinks in terms of UDP “socket
names” that are always a pair linking an IP interface—even if it is the wildcard interface—with a UDP
port number. It is these socket names that must not conflict among the listening servers at any given
moment, rather than the bare UDP ports that are in use.
