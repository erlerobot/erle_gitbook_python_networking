## Threading and Multi-processing Frameworks

The [SocketServer module](https://docs.python.org/2/library/socketserver.html?highlight=socketserver#SocketServer) simplifies the task of writing network servers.

There are four basic server classes: TCPServer, UDPServer,UnixDatagramServer  and UnixStreamServer.

These four classes process requests synchronously; each request must be completed before the next request can be started. This isn’t suitable if each request takes a long time to complete, because it requires a lot of computation, or because it returns a lot of data which the client is slow to process.

In `server_SocketServer.py`, you can see how small our multi-threaded server becomes when it takes
advantage of this framework. (There is also a ForkingMixIn that you can use if you want it to spawn
several processes—at least on a POSIX system.)

```python

from SocketServer import ThreadingMixIn, TCPServer, BaseRequestHandler
import lancelot, server_simple, socket

class MyHandler(BaseRequestHandler):
    def handle(self):
        server_simple.handle_client(self.request)

class MyServer(ThreadingMixIn, TCPServer):
    allow_reuse_address = 1
    # address_family = socket.AF_INET6  # if you need IPv6

server = MyServer(('', lancelot.PORT), MyHandler)
server.serve_forever()
```

Whereas our earlier example created the workers up front so that they were all sharing the same
listening socket, the SocketServer does all of its listening in the main thread and creates one worker each
time `accept()` returns a new client socket.
