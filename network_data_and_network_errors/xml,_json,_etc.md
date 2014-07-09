## XML, JSON, Etc.

If your protocol needs to be usable from other programming languages—or if you simply prefer
universal standards to formats specific to Python—then the JSON and XML data formats are each a
popular choice. Note that neither of these formats supports framing, so you will have to first figure out
how to extract a complete string of text from over the network before you can then process it.

JSON is among the best choices available today for sending data between different computer
languages. Since Python 2.6, it has been included in the Standard Library as a [module named `json`](https://docs.python.org/2/library/json.html?highlight=json#json).
JSON, short for JavaScript Object Notation, is a lightweight format for data exchange. JSON is a subset of the object literal notation JavaScript that does not require the use of XML.
For ncoding basic Python object hierarchies:

```python
>>> #The syntaxis is:
...
>>> import json
>>> json.dumps(['foo', {'bar': ('baz', None, 1.0, 2)}])
'["foo", {"bar": ["baz", null, 1.0, 2]}]'
>>> #Example:
...
>>> json.dumps([ 51, u'Namárië!' ])
'[51, "Nam\\u00e1ri\\u00eb!"]'
```

For decoding it you should use:
```python

>>> #The syntaxis is:
...
>>> import json
>>> json.loads('["foo", {"bar":["baz", null, 1.0, 2]}]')
[u'foo', {u'bar': [u'baz', None, 1.0, 2]}]
>>> #An example:
...
>>> json.loads('{"name": "Lancelot", "quest": "Grail"}')
{u'quest': u'Grail', u'name': u'Lancelot'}
```

Note that the protocol fully supports Unicode strings.
It does, however, have a weakness: a vast omission in the JSON standard is that it provides
absolutely no provision for cleanly passing binary data like images or arbitrary documents.The XML format is better for documents, since its basic structure is to take strings and mark them
up by wrapping them in angle-bracketed elements.
