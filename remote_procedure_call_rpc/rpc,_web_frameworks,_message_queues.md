## RPC, Web Frameworks, Message Queues

Be willing to explore alternative transmission mechanisms for your work with RPC services. The classes
provided in the Python Standard Library for XML-RPC, for example, are not even used by many Python
programmers who need to speak that protocol.

There are three useful ways that you can look into moving beyond overly simple example code that
makes it look as though you have to bring up a new web server for every RPC service you want to make
available from a particular site.

First, look into whether you can use the pluggability of WSGI to let you install an RPC service that
you have incorporated into a larger web project that you are deploying. Implementing both your normal
web application and your RPC service as WSGI servers beneath a filter that checks the incoming URL
enables you to allow both services to live at the same hostname and port number.

Second, instead of using a dedicated RPC library, you may find that your web framework of choice
already knows how to host an XML-RPC, JSON-RPC, or some other flavor of RPC call.

Third, you might want to try sending RPC messages over an alternate transport that does a better
job than the protocol’s native transport of routing the calls to servers that are ready to handle them.
Message queues are often an excellent vehicle for RPC calls when you
want a whole rack of servers to stay busy sharing the load of incoming requests.

######Recovering From Network Errors

Of course, there is one reality of life on the network that RPC services cannot easily hide: the network can
be down or even go down in the middle of a particular RPC call.
You will find that most RPC mechanisms simply raise an exception if a call is interrupted and does
not complete. Note that an error, unfortunately, is no guarantee that the remote end did not process the
request — maybe it actually did finish processing it, but then the network went down right as the last
packet of the reply was being sent. In this case, your call would have technically happened and the data
would have been successfully added to the database or written to a file or whatever the RPC call does.
However, you will think the call failed and want to try it again — possibly storing the same data twice.

######Binary Options: Thrift and Protocol Buffers

It is possible you will want both features: a compact and efficient binary format and support across
several different languages. Here are a few options:

- Some JSON-RPC libraries support the BSON protocol, which provides a tight
binary transport format and also an expanded range of data types beyond those
supported by JSON.


- The Apache Foundation is now incubating Thrift, an RPC system developed
several years ago at Facebook and released as open source.

- Google Protocol Buffers are popular with many programmers, but strictly
speaking they are not a full RPC system; instead, they are a binary data
serialization protocol.
