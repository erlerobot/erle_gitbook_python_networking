## Using TCP Streams like Files

Since TCP supports streams of data, they might have already reminded you of normal files, which also
support reading and writing as fundamental operations. Python does a very good job of keeping these
concepts separate: file objects can `read()` and `write()`, sockets can `send()` and `recv(), and no kind of
object can do both.
But sometimes you will want to treat a socket like a normal Python file object—often because you
want to pass it into code like that of the many Python modules such as pickle, json, and zlib that can
read and write data directly from a file. For this purpose, Python provides a makefile() method on every
socket that returns a Python file object that is really calling recv() and send() behind the scenes:
```python
import socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
hasattr(s, 'read')
False
f = s.makefile()
hasattr(f, 'read')
True
```
Sockets, like normal Python files, also have a `fileno()` method that lets you discover their file
descriptor number in case you need to supply it to lower-level calls.
