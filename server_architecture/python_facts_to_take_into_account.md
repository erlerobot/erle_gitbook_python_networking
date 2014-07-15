##Twisted Python

There are a couple of Python facts to take into account when you are computing your own event-driven server.

It happens that Python comes with an event-driven framework built into the Standard Library. I am going to recommend
that you ignore it entirely. It is a pair of ancient modules, asyncore and asynchat, that date from the early
days of Python—you will note that all of the classes they define are lowercase, in defiance of both good
taste and all subsequent practice—and that they are difficult to use correctly.

Instead, we will talk about [Twisted Python](https://pypi.python.org/pypi/Twisted).
Twisted Python is not simply a framework; it is an event-driven networking engine for Python.
The Twisted community
has developed a way of writing Python that is all their own.

Take a look at `server_twisted.py` for how simple our
event-driven server can become if we leave the trouble of dealing with the low-level operating system
calls to someone else.
```python

from twisted.internet.protocol import Protocol, ServerFactory
from twisted.internet import reactor
import lancelot

class Lancelot(Protocol):
    def connectionMade(self):
        self.question = ''

    def dataReceived(self, data):
        self.question += data
        if self.question.endswith('?'):
            self.transport.write(dict(lancelot.qa)[self.question])
            self.question = ''

factory = ServerFactory()
factory.protocol = Lancelot
reactor.listenTCP(1060, factory)
reactor.run()
```

From then on, every
event on that socket is translated into a method call to our object, letting us write code that appears to be
thinking about just one client at a time. But thanks to the fact that Twisted will create dozens or
hundreds of our Launcelot protocol objects, one corresponding to each connected client, the result is an
event loop that can respond to whichever client sockets are ready.

[Here](http://twistedmatrix.com/documents/current/core/howto/) you can find more infromation about Twisted Python
