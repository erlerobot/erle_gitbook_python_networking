## Disconnecting


Strictly speaking, you’re supposed to use `shutdown` on a socket before you close it. The `shutdown` is an advisory to the socket at the other end. Depending on the argument you pass it, it can mean “I’m not going to send anymore, but I’ll still listen”, or “I’m not listening, good riddance!”. Most socket libraries, however, are so used to programmers neglecting to use this piece of etiquette that normally a close is the same as `shutdown()`; `close()`. So in most situations, an explicit shutdown is not needed.

One way to use `shutdown` effectively is in an HTTP-like exchange. The client sends a request and then does a `shutdown(1)`. This tells the server “This client is done sending, but can still receive.” The server can detect “EOF” by a receive of 0 bytes. It can assume it has the complete request. The server sends a reply. If the send completes successfully then, indeed, the client was still receiving.

Python takes the automatic shutdown a step further, and says that when a socket is garbage collected, it will automatically do a close if it’s needed. But relying on this is a very bad habit. If your socket just disappears without doing a close, the socket at the other end may hang indefinitely, thinking you’re just being slow. So, it is very recommendable close your sockets when you’re done.



