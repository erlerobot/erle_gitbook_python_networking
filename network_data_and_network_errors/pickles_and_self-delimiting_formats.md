##Pickles and Self-Delimiting Formats

Note that some kinds of data that you might send across the network already include some form of
delimiting built-in. If you are transmitting such data, then you might not have to impose your own
framing atop what the data is already doing.
Consider Python “pickles” for example, the native form of serialization that comes with the
Standard Library. The [pickle module](https://docs.python.org/2/library/pickle.html?highlight=pickles) implements a fundamental, but powerful algorithm for serializing and de-serializing a Python object structure. “Pickling” is the process whereby a Python object hierarchy is converted into a byte stream, and “unpickling” is the inverse operation, whereby a byte stream is converted back into an object hierarchy.Moreover, using a quirky mix of text commands and data, a pickle stores the contents of a Python
data structure so that you can reconstruct it later or on a different machine:
```python
>>> import pickle
>>> pickle.dumps([5, 6, 7])
'(lp0\nI5\naI6\naI7\na.'
```
The interesting thing about the format is the '.' character that you see at the end of the foregoing
string—it is the format's way of marking the end of a pickle. Upon encountering it, the loader can stop
and return the value without reading any further. Thus we can take the foregoing pickle, stick some ugly
data on the end, and see that `loads()` will completely ignore it and give us our original list back:
```python
>>> pickle.loads('(lp0\nI5\naI6\naI7\na.UjJGdVpHRnNaZz09')
[5, 6, 7]
```
Of course, using `loads()` this way is not useful for network data, since it does not tell us how many
bytes it processed in order to reload the pickle; we still do not know how much of our string is pickle
data. But if we switch to reading from a file and using the pickle `load()` function, then the file pointer will
be left right at the end of the pickle data, and we can start reading from there if we want to read what
came after the pickle:
```python
>>> from StringIO import StringIO
>>> f = StringIO('(lp0\nI5\naI6\naI7\na.UjJGdVpHRnNaZz09')
>>> pickle.load(f)
[5, 6, 7]
>>> f.pos
18
>>> f.read()
'UjJGdVpHRnNaZz09'
```

