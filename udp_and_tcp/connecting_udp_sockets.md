## Connecting UDP Sockets

The remote UDP client in `socket1.py` uses a new call that we have not discussed before: the `connect()`
socket operation. You can see easily enough what it does. Instead of having to use `sendto()` and an
explicit UDP address every time we want to send something to the server, the `connect()` call lets the
operating system know ahead of time which remote address to which we want to send packets, so that
we can simply supply data to the `send()` call and not have to repeat the server address again.
But `connect()` does something else important, which will not be obvious at all from reading the script of
`udp_remote.py`.
To approach this topic, let us return to `udp_local.py`file for a moment. You will recall that both its client
and server use the loopback IP address and assume reliable delivery—the client will wait forever for a
response. Try running the client in one window:
```
root@erlerobot:~/Python_files# python udp_local.py
Address before sending: ('0.0.0.0', 0)
Address after sending ('0.0.0.0', 52970)
```
The client is now waiting—perhaps forever—for a response in reply to the packet it has just sent to
the localhost IP address at UDP port 1060. But what if we nefariously try sending it back a packet from a
different server, instead?
From another command prompt on the same system, try running Python and entering these
commands—and for the port number, copy the integer that was just printed to the screen when you ran
the UDP client:
```python
>>> import socket
>>> s=socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
>>> s.sendto('Fake reply',('127.0.0.1',52970))
10
>>>

```
In the client window appears:
```
The server ('127.0.0.1', 65320) says 'Fake reply'
```
It turns out that our first client accepts answers from anywhere. Even though the server is
running on the localhost, and remote network connectivity is not even desirable, the
client will even accept packets from another machine. If I bring up a Python prompt on another box and
run the same two lines of code as just shown, then a waiting client can even see the remote IP address.

There are, then, two ways to write UDP clients that are careful about the return addresses of the
packets arriving back:

- You can use `sendto()` and direct each outgoing packet to a specific destination,
and then use `recvfrom()` to receive the replies and carefully check the return
address it gives you against the list of servers to which you have made outstanding
requests.


- You can `connect()` your socket right after creating it, and then simply use `send()`
and `recv()`, and the operating system will filter out unwanted packets for you. This
works only for speaking to one server at a time, because running `connect()` a
second time on the same socket does not add a second destination address to
your UDP socket. Instead, it wipes out the first address entirely, so that no further
replies from the earlier address will be delivered to your program.
