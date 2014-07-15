## Message Queues

Message queue protocols let you send reliable chunks of data called messages. Typically, a
queue promises to transmit messages reliably, and to deliver them atomically: a message either arrives
whole and intact, or it does not arrive at all. Clients never have to loop and keep calling something like
`recv()` until a whole message has arrived.
The other innovation that message queues offer is that, instead of supporting only the point-topoint
connections that are possible with an IP transport like TCP, you can set up all kinds of topologies
between messaging clients. Each brand of message queue typically supports several topologies.

A pipeline topology is the pattern that perhaps best resembles the picture you have in your head
when you think of a queue: a producer creates messages and submits them to the queue, from which the
messages can then be received by a consumer. For example, the front-end web machines of a photosharing
web site might accept image uploads from end users and list the incoming files on an internal
queue. A machine room full of servers could then read from the queue, each receiving one message for
each read it performs, and generate thumbnails for each of the incoming images. The queue might get
long during the day and then be short or empty during periods of relatively low use, but either way the
front-end web servers are freed to quickly return a page to the waiting customer, telling them that their
upload is complete and that their images will soon appear in their photostream.

A publisher-subscriber topology looks very much like a pipeline, but with a key difference. The
pipeline makes sure that every queued message is delivered to exactly one consumer—since, after all, it
would be wasteful for two thumbnail servers to be assigned the same photograph. But subscribers
typically want to receive all of the messages that are being enqueued by each publisher—or else they
want to receive every message that matches some particular topic. Either way, a publisher-subscriber
model supports messages that fan out to be delivered to every interested subscriber. This kind of queue
can be used to power external services that need to push events to the outside world, and also to form a
fabric that a machine room full of servers can use to advertise which systems are up, which are going
down for maintenance, and that can even publish the addresses of other message queues as they are
created and destroyed.

![messages](./mqueu.png)

Finally, a request-reply pattern is often the most complex because messages have to make a roundtrip.
Both of the previous patterns placed very little responsibility on the producer of a message: they
connect to the queue, transmit their message, and are done. But a message queue client that makes a
request has to stay connected and wait for the corresponding reply to be delivered back to it. The queue
itself, to support this, has to feature some sort of addressing scheme by which replies can be directed to
the correct client that is still sitting and waiting for it. But for all of its underlying complexity, this is
probably the most powerful pattern of all, since it allows the load of dozens or hundreds of clients to be
spread across equally large numbers of servers without any effort beyond setting up the message queue.
And since a good message queue will allow servers to attach and detach without losing messages, this
topology allows servers to be brought down for maintenance in a way that is invisible to the population
of client machines.

