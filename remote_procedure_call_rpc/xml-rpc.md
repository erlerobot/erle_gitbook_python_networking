## XML-RPC

XML-RPC has native support in Python precisely because it was one of the first RPC protocols of
the Internet age, operating natively over HTTP instead of insisting on its own on-the-wire protocol. This
means our examples will not even require any third-party modules. While we will see that this makes our
RPC server somewhat less capable than if we moved to a third-party library, this will also make the
examples good ones for an initial foray into RPC.

If you have ever used raw XML, then you are familiar with the fact that it lacks any data-type
semantics; it cannot represent numbers, for example, but only elements that contain other elements, text
strings, and text-string attributes. Thus the XML-RPC specification has to build additional semantics on
top of the plain XML document format in order to specify how things like numbers should look when
converted into marked-up text.
The Python Standard Library makes it easy to write either an XML-RPC client or server, though more
power is available when writing a client. For example, the client library supports HTTP basic
authentication, while the server does not support this. Therefore, we will begin at the simple end, with
the server.


`xmlrpc_server.py` shows a basic server that starts a web server on port 7001 and listens for incoming
Internet connections.Here we eill use the [operator module](https://docs.python.org/2.7/library/operator.html?highlight=operator#operator), which exports a set of efficient functions corresponding to the intrinsic operators of Python.

```python


import operator, math
from SimpleXMLRPCServer import SimpleXMLRPCServer

def addtogether(*things):
    """Add together everything in the list `things`."""
    return reduce(operator.add, things)

def quadratic(a, b, c):
    """Determine `x` values satisfying: `a` * x*x + `b` * x + c == 0"""
    b24ac = math.sqrt(b*b - 4.0*a*c)
    return list(set([ (-b-b24ac) / 2.0*a,
                      (-b+b24ac) / 2.0*a ]))

def remote_repr(arg):
    """Return the `repr()` rendering of the supplied `arg`."""
    return arg

server = SimpleXMLRPCServer(('127.0.0.1', 7001))
server.register_introspection_functions()
server.register_multicall_functions()
server.register_function(addtogether)
server.register_function(quadratic)
server.register_function(remote_repr)
print "Server ready"
server.serve_forever()
```

You can see that the three sample functions that the server offers over XML-RPC — the ones that are
added to the RPC service through the `register_function()` calls — are quite typical Python functions.
And that, again, is the whole point of XML-RPC: it lets you make routines available for invocation over
the network without having to write them any differently than if they were normal functions offered
inside of your program.

Note that two additional configuration calls are made in addition to the three calls that register our
functions. Each of them turns on an additional service that is optional, but often provided by XML-RPC
servers: an introspection routine that a client can use to ask which RPC calls are supported by a given
server; and the ability to support a multicall function that lets several individual function calls be
bundled together into a single network round-trip.
This server will need to be running before we can try any of the next three program listings, so bring
up a command window and get it started:

```
root@erlerobot:~/Python_files# python xmlrpc_server.py
Server ready
```
This means that hee server is now waiting for connections on localhost port 7001.

Now, open another command window and get
ready to try out the next three listings as we review them.
First, we will try out the introspection capability that we turned on in this particular server. Note
that this ability is optional, and it may not be available on many other XML-RPC services that you use
online or that you deploy yourself. `xmlrpc_introspect.py` shows how introspection happens from the client’s point
of view.
```python


import xmlrpclib
proxy = xmlrpclib.ServerProxy('http://127.0.0.1:7001')

print 'Here are the functions supported by this server:'
for method_name in proxy.system.listMethods():

    if method_name.startswith('system.'):
        continue

    signatures = proxy.system.methodSignature(method_name)
    if isinstance(signatures, list) and signatures:
        for signature in signatures:
            print '%s(%s)' % (method_name, signature)
    else:
        print '%s(...)' % (method_name,)

    method_help = proxy.system.methodHelp(method_name)
    if method_help:
        print '  ', method_help
        ```
 The introspection mechanism is an optional extension that is not actually defined in the XML-RPC
specification itself. The client is able to call a series of special methods that all begin with the string
system. to distinguish them from normal methods. These special methods give information about the
other calls available. We start by calling `listMethods()`. If introspection is supported at all, then we will
receive back a list of other method names; for this example listing, we ignore the system methods and
only proceed to print out information about the other ones.
In the `xmlrpc_introspect.py` we use the [xmlrpc module](https://docs.python.org/2.7/library/xmlrpclib.html?highlight=xmlrpclib#xmlrpclib), this module supports writing XML-RPC client code; it handles all the details of translating between conformable Python objects and XML on the wire.
```
root@erlerobot:~/Python_files# python xmlrpc_introspect.py
Here are the functions supported by this server:
addtogether(...)
   Add together everything in the list `things`.
quadratic(...)
   Determine `x` values satisfying: `a` * x*x + `b` * x + c == 0
remote_repr(...)
   Return the `repr()` rendering of the supplied `arg`.
   ```

You will recall that the whole point of an RPC service is to make function calls in a target language look
as natural as possible. And as you can see in ` xmlrpc_client.py` the Standard Library’s xmlrpclib gives you a
proxy object for making function calls against the server. These calls look exactly like local function calls.
```python

import xmlrpclib
proxy = xmlrpclib.ServerProxy('http://127.0.0.1:7001')
print proxy.addtogether('x', 'ÿ', 'z')
print proxy.addtogether(20, 30, 4, 1)
print proxy.quadratic(2, -4, 0)
print proxy.quadratic(1, 2, 1)
print proxy.remote_repr((1, 2.0, 'three'))
print proxy.remote_repr([1, 2.0, 'three'])
print proxy.remote_repr({'name': 'Arthur', 'data': {'age': 42, 'sex': 'M'}})
print proxy.quadratic(1, 0, 1)
```
Note how almost all of the
calls work without a hitch, and how both of the calls in this listing and the functions themselves back in
`xmlrpc_server.py` look like completely normal Python; there is with nothing about them that is particular to a
network:

```
root@erlerobot:~/Python_files# python xmlrpc_client.py
xÿz
55
[0.0, 8.0]
[-1.0]
[1, 2.0, 'three']
[1, 2.0, 'three']
{'data': {'age': [42], 'sex': 'M'}, 'name': 'Arthur'}
Traceback (most recent call last):
...
xmlrpclib.Fault: <Fault 1: "<type 'exceptions.ValueError'>:math domain error">
```
Note that XML-RPC function calls, like those of Python and many other languages in its
lineage, can take several arguments, but can only return a single result value. That value might be a
complex data structure, but it will be returned as a single result. And the protocol does not care whether
that result has a consistent shape or size; the list returned by `quadratic()` varies in its
number of elements returned without any complaint from the network logic.Note, also,that the rich variety of Python data types must be reduced to the smaller set that XMLRPC
itself happens to support. In particular, XML-RPC only supports a single sequence type: the list.

Thus far we have covered the general features and restrictions of XML-RPC. If you consult the
documentation for either the client or the server module in the Standard Library, you can learn about a
few more features. In particular, you can learn how to use TLS and authentication by supplying more
arguments to the ServerProxy class. But one feature is important enough to go ahead and cover here: the
ability to make several calls in a network round-trip when the server supports it , as shown in `xmlrpc_multicall.py`.
```python
import xmlrpclib
proxy = xmlrpclib.ServerProxy('http://127.0.0.1:7001')
multicall = xmlrpclib.MultiCall(proxy)
multicall.addtogether('a', 'b', 'c')
multicall.quadratic(2, -4, 0)
multicall.remote_repr([1, 2.0, 'three'])
for answer in multicall():
    print answer
    ```
When you run this script, you can carefully watch the server’s command window to confirm that
only a single HTTP request is made in order to answer all three function calls that get made.

Three final points are worth mentioning before we move on to examining another RPC mechanism:
- There are two additional data types that sometimes prove hard to live without, so
many XML-RPC mechanisms support them: dates and the value that Python calls
None . Python’s client and server both
support options that will enable the transmission and reception of these nonstandard
types.


- Keyword arguments are, alas, not supported by XML-RPC, because few languages
are sophisticated enough to include them and XML-RPC wants to interoperate
with those languages. Some services get around this by allowing a dictionary to be
passed as a function’s final argument .


- Finally, keep in mind that dictionaries can only be passed if all of their keys are
strings, whether normal or Unicode. See the “Self-documenting Data” section
later in this chapter for more information on how to think about this restriction.

