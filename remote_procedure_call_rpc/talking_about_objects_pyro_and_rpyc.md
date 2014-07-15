## Talking About Objects: Pyro and RPyC

If the idea of RPC was to make remote function calls look like local ones, then the two basic RPC
mechanisms we have looked at actually fail pretty spectacularly. If the functions we were calling
happened to only use basic data types in their arguments and return values, then XML-RPC and JSONRPC
would work fine. But think of all of the occasions when you use more complex parameters and
return values instead! What happens when you need to pass live objects?

When all you have are Python programs that need to talk to each other, there is at least one
excellent reason to look for an RPC service that knows about Python objects and their ways: Python has a
number of very powerful data types, so it can simply be unreasonable to try “talking down” to the dialect
of limited data formats like XML-RPC and JSON-RPC. This is especially true when Python dictionaries,
sets, and datetime objects would express exactly what you want to say.
There are two Python-native RPC systems that we should mention: Pyro and RPyC.
The Pyro project lives here: http://ww.xs4all.nl/~irmen/pyro3/

This well-established RPC library is built on top of the Python pickle module, and it can send any
kind of argument and response value that is inherently pickle-able. Basically, this means that, if an
object) and its attributes) can be reduced to its basic types, then it can be transmitted. However, if the
values you want to send or receive are ones that the [pickle module](https://docs.python.org/2.7/library/pickle.html?highlight=pickle#pickle) chokes on, then Pyro will not work
for your situation.The `pickle` module implements a fundamental, but powerful algorithm for serializing and de-serializing a Python object structure. “Pickling” is the process whereby a Python object hierarchy is converted into a byte stream, and “unpickling” is the inverse operation, whereby a byte stream is converted back into an object hierarchy.
