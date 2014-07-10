## Using Message Queues from Python

There are several AMQP (Advanced Message
Queuing Protocol) implementations currently listed in the Python Package Index.

An alternative to using AMQP and having to run a central broker, like RabbitMQ or Apache Qpid, is
to use ØMQ, the “Zero Message Queue,” which was invented by the same company as AMQP but moves
the messaging intelligence from a centralized broker into every one of your message client programs.

A good summary of the advantages and disadvantages is
provided at the ØMQ web site: http://zeromq.org/docs:welcome-from-amqp

The next example,`queuecrazy.py`, shows some of the patterns that can be supported when message queues are used to
connect different parts of an application. It requires [ØMQ](https://pypi.python.org/pypi/pyzmq-static/2.2), which you can most easily make available to
Python Index:
```
root@erlerobot:~/Python_files# pip install pyzmq-static
```
The listing uses Python threads to create a small cluster of six different services. One pushes a
constant stream of words on to a pipeline. Three others sit ready to receive a word from the pipeline;
each word wakes one of them up. The final two are request-reply servers, which resemble remote
procedure endpoints and send back a message for each message they receive.

```python

import random, threading, time, zmq
zcontext = zmq.Context()

def fountain(url):
    """Produces a steady stream of words."""
    zsock = zcontext.socket(zmq.PUSH)
    zsock.bind(url)
    words = [ w for w in dir(__builtins__) if w.islower() ]
    while True:
        zsock.send(random.choice(words))
        time.sleep(0.4)

def responder(url, function):
    """Performs a string operation on each word received."""
    zsock = zcontext.socket(zmq.REP)
    zsock.bind(url)
    while True:
        word = zsock.recv()
        zsock.send(function(word))  # send the modified word back

def processor(n, fountain_url, responder_urls):
    """Read words as they are produced; get them processed; print them."""
    zpullsock = zcontext.socket(zmq.PULL)
    zpullsock.connect(fountain_url)

    zreqsock = zcontext.socket(zmq.REQ)
    for url in responder_urls:
        zreqsock.connect(url)

    while True:
        word = zpullsock.recv()
        zreqsock.send(word)
        print n, zreqsock.recv()

def start_thread(function, *args):
    thread = threading.Thread(target=function, args=args)
    thread.daemon = True  # so you can easily Control-C the whole program
    thread.start()

start_thread(fountain, 'tcp://127.0.0.1:6700')
start_thread(responder, 'tcp://127.0.0.1:6701', str.upper)
start_thread(responder, 'tcp://127.0.0.1:6702', str.lower)
for n in range(3):
    start_thread(processor, n + 1, 'tcp://127.0.0.1:6700',
                 ['tcp://127.0.0.1:6701', 'tcp://127.0.0.1:6702'])
time.sleep(30)
```


The two request-reply servers are different—one turns each word it receives to uppercase, while the
other makes its words all lowercase—and you can tell the three processors apart by the fact that each is
assigned a different integer.


Finally I would like to add the following to fix the concept of message Queues:
Message queues provide a point of coordination and integration for different parts of your
application that may require different hardware, load balancing techniques, platforms, or even
programming languages. They can take responsibility for distributing messages among many waiting
consumers or servers in a way that is not possible with the single point-to-point links offered by normal
TCP sockets, and can also use a database or other persistent storage to assure that updates to your
service are not lost if the server goes down. Message queues also offer resilience and flexibility, since if
some part of your system temporarily becomes a bottleneck, then the message queue can absorb the
shock by allowing many messages to queue up for that service. By hiding the population of servers or
processes that serve a particular kind of request, the message queue pattern also makes it easy to
disconnect, upgrade, reboot, and reconnect servers without the rest of your infrastructure noticing.
