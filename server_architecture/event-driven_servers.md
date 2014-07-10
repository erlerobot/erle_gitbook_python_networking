## Event-Driven Servers

The simple server we have been examining has the problem that the `recv() call often finds that no data
is yet available from the client, so the call “blocks” until data arrives. The time spent waiting, as we have
seen, is time lost; it cannot be spent usefully by the server to answer requests from other clients.

But what if we avoided ever calling `recv()` until we knew that data had arrived from a particular
client. The result would be an eventdriven
server that sits in a tight loop watching many clients; I have written an example, shown in
`server_poll.`
```python

import lancelot
import select

listen_sock = lancelot.setup()
sockets = { listen_sock.fileno(): listen_sock }
requests = {}
responses = {}

poll = select.poll()
poll.register(listen_sock, select.POLLIN)

while True:
    for fd, event in poll.poll():
        sock = sockets[fd]

        # Removed closed sockets from our list.
        if event & (select.POLLHUP | select.POLLERR | select.POLLNVAL):
            poll.unregister(fd)
            del sockets[fd]
            requests.pop(sock, None)
            responses.pop(sock, None)

        # Accept connections from new sockets.
        elif sock is listen_sock:
            newsock, sockname = sock.accept()
            newsock.setblocking(False)
            fd = newsock.fileno()
            sockets[fd] = newsock
            poll.register(fd, select.POLLIN)
            requests[newsock] = ''

        # Collect incoming data until it forms a question.
        elif event & select.POLLIN:
            data = sock.recv(4096)
            if not data:      # end-of-file
                sock.close()  # makes POLLNVAL happen next time
                continue
            requests[sock] += data
            if '?' in requests[sock]:
                question = requests.pop(sock)
                answer = dict(lancelot.qa)[question]
                poll.modify(sock, select.POLLOUT)
                responses[sock] = answer

        # Send out pieces of each reply until they are all sent.
        elif event & select.POLLOUT:
            response = responses.pop(sock)
            n = sock.send(response)
            if n < len(response):
                responses[sock] = response[n:]
            else:
                poll.modify(sock, select.POLLIN)
                requests[sock] = ''
                ```

The main loop in this program is controlled by the poll object, which is queried at the top of every
iteration. The `poll()` call is a blocking call, the difference is that recv() has to wait on one single client,
while `poll()` can wait on dozens or hundreds of clients, and return when any of them shows activity.

The way `poll()` works is that we tell it which sockets we need to monitor, and whether each socket
interests us because we want to read from it or write to it. When one or more of the sockets are ready,
`poll()` returns and provides a list of the sockets that we can now use.

To keep things straight when reading the code, think about the lifespan of one particular client and
trace what happens to its socket and data.

- The client will first do a `connect()`, and the server’s `poll()` call will return and
declare that there is data ready on the main listening socket. That can mean
only one thing, a new client has
connected. So we `accept()` the connection and tell our poll object that we want
to be notified when data becomes available for reading from the new socket. To
make sure that the `recv()` and `send()` methods on the socket never block and
freeze our event loop, we call the `setblocking()` socket method with the value
False (which means “blocking is not allowed”).

- When data becomes available, the incoming string is appended to whatever is
already in the requests dictionary under the entry for that socket. ( sockets
can safely be used as dictionary keys in Python)

- We keep accepting more data until we see a question mark, at which point the
Launcelot question is complete. The questions are so short that, in practice,
they probably all arrive in the very first `recv()` from each socket; but just to be
safe, we have to be prepared to make several `recv()` calls until the whole
question has arrived. We then look up the appropriate answer, store it in the
responses dictionary under the entry for this client socket, and tell the poll
object that we no longer want to listen for more data from this client but
instead want to be told when its socket can start accepting outgoing data.
- Once a socket is ready for writing, we send as much of the answer as will fit into
one `send() `call on the client socket. This, by the way, is a big reason `send()`
returns a length: because if you use it in non-blocking mode, then it might be
able to send only some of your bytes without making you wait for a buffer to
drain back down.

- Once this server has finished transmitting the answer, we tell the poll object to
swap the client socket back over to being listened to for new incoming data.

- After many question-answer exchanges, the client will finally close the
connection. Oddly enough, the POLLHUP, POLLERR, and POLLNVAL circumstances
that `poll()` can tell us about—all of which indicate that the connection has
closed one way or another—are returned only if we are trying to write to the
socket, not read from it. So when an attempt to read returns zero bytes, we have
to tell the poll object that we now want to write to the socket so that we receive
the official notification that the connection is closed.

###### Two things you should know

- A slightly older mechanism for writing event-driven servers that listen to sockets is to use the `select()
` call, which like `poll()` is available from the Python select module in the Standard Library. I recommend to use `poll() because it produces much cleaner code, but many people choose select() because it is supported on Windows.

- When talking about event-driven servers, you should take into account the following: **Event-Driven Servers are Blocking and Synchronous**.
Referring to the event - driven servers, like the one in `server_poll.py`, some people call them “non-blocking,” despite the fact that the `poll()` call blocks(they mean that it does
not block waiting for any particular client), and others
call them “asynchronous” despite the fact that the program executes its statements in their usual linear
order.
