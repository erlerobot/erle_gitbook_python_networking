## JSON-RPC

The bright idea behind JSON is to serialize data structures to strings that use the syntax of the JavaScript
programming language. This means that JSON strings can be turned back into data in a web browser
simply by using the `eval()` function. By using a syntax specifically designed for data rather than adapting
a verbose document markup language like XML, this remote procedure call mechanism can make your
data much more compact while simultaneously simplifying your parsers and library code.

JSON-RPC is not supported in the Python Standard Library, so you
will have to choose one of the several third-party distributions available. You can find these distributions
on the Python Package Index. My own favorite is [lovely.jsonrpc](https://pypi.python.org/pypi/lovely.jsonrpc/0.2.1). If you install it in a virtual environment, then you
can try out the server and client shown in Listings `jsonrpc_server.py` and `jsonrpc_client.py`.
```python

from wsgiref.simple_server import make_server
import lovely.jsonrpc.dispatcher, lovely.jsonrpc.wsgi

def lengths(*args):
    results = []
    for arg in args:
        try:
            arglen = len(arg)
        except TypeError:
            arglen = None
        results.append((arglen, arg))
    return results

dispatcher = lovely.jsonrpc.dispatcher.JSONRPCDispatcher()
dispatcher.register_method(lengths)
app = lovely.jsonrpc.wsgi.WSGIJSONRPCApplication({'': dispatcher})
server = make_server('localhost', 7002, app)
print "Starting server"
while True:
    server.handle_request()
    ```

The server code is quite simple, as an RPC mechanism should be. As with XML-RPC, we merely need
to name the functions that we want offered over the network, and they become available for queries.

```python
from lovely.jsonrpc import proxy
proxy = proxy.ServerProxy('http://localhost:7002')
print proxy.lengths((1,2,3), 27, {'Sirius': -1.46, 'Rigel': 0.12})
```

First, note that the protocol allowed us to send as many arguments as we wanted; it was not
bothered by the fact that it could not introspect a static method signature from our function.Second, note that the None value in the server’s reply passes back to us unhindered.

```
root@erlerobot:~/Python_files# python jsonrpc_server.py
Starting server
[In another command window:]
$ python jsonrpc_client.py
[[3, [1, 2, 3]], [None, 27], [2, {'Rigel': 0.12, 'Sirius': -1.46}]]
``
