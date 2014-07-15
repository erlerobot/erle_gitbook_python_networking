## The Semantics of Non-blocking

I should add a quick note about how `recv()` and `send()` behave in non-blocking mode, when you have
called setblocking(False) on their socket. A `poll() loop like the one just shown means that we never
finish calling either of these functions when they cannot accept or provide data. But what if we find
ourselves in a situation where we want to call either function in non-blocking mode and do not yet know
whether the socket is ready?

For the `recv()` call, these are the rules:
- If data is ready, it is returned.
- If no data has arrived, socket.error is raised.
- If the connection has closed, '' is returned.


Note that closed connection returns a value, but a still-open connection
raises an exception. The logic behind this behavior is that the first and last possibilities are both possible
in blocking mode as well: either you get data back, or finally the connection closes and you get back an
empty string. So to communicate the extra, third possibility that can happen in non-blocking mode—
that the connection is still open but no data is ready yet—an exception is used.

The behavior of non-blocking `send()` is similar:
- Some data is sent, and its length is returned.
- The socket buffers are full, so socket.error is raised.
- If the connection is closed, socket.error is also raised.

This evidence that
`poll()` could say that a socket is ready for sending, but a FIN packet from the client could arrive right
after the server is released from its `poll()` but before it can start up its `send()` call.

