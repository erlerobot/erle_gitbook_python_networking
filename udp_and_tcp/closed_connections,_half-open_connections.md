## Closed Connections, Half-Open Connections

`tcp_deadlock.py` shows us how a Python socket object behaves when an end-of-file is reached.You will see that the client makes a shutdown() call on the socket after it finishes sending its
transmission. This solves an important problem: if the server is going to read forever until it sees end-offile,
then how will the client avoid having to do a full close() on the socket and thus forbid itself from
doing the many recv() calls that it still needs to make to receive the server’s response? The solution is to
“half-close” the socket—that is, to permanently shut down communication in one direction but without
destroying the socket itself—so that the server can no longer read any data, but can still send any
remaining reply back in the other direction, which will still be open.
The shutdown() call can be used to end either direction of communication in a two-way socket like
this; its argument can be one of three symbols:
- SHUT_WR: This is the most common value used, since in most cases a program
knows when its own output is finished but not about when its conversation
partner will be done. This value says that the caller will be writing no more data
into the socket, and that reads from its other end should act like it is closed.

- SHUT_RD: This is used to turn off the incoming socket stream, so that an end-of-file
error is encountered if your peer tries to send any more data to you on the socket.

- SHUT_RDWR: This closes communication in both directions on the socket. It might
not, at first, seem useful, because you can also just perform a `close()` on the
socket and communication is similarly ended in both directions. The difference is
a rather advanced one: if several programs on your operating system are allowed
to share a single socket, then `close()` just ends your process’s relationship with
the socket, but keeps it open as long as another process is still using it; but
`shutdown() will always immediately disable the socket for everyone using it.
