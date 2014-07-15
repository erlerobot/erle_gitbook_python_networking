## Self-documenting Data

You have just seen that both XML-RPC and JSON-RPC appear to support a data structure very much like
a Python dictionary, but with an annoying limitation. In XML-RPC, the data structure is called a struct,
whereas JSON calls it an object. To the Python programmer, however, it looks like a dictionary, and your
first reaction will probably be annoyance that its keys cannot be integers, floats, or tuples.
Let us look at a concrete example. Imagine that you have a dictionary of physical element symbols
indexed by their atomic number:
```python
{1: 'H', 2: 'He', 3: 'Li', 4: 'Be', 5: 'B', 6: 'C', 7: 'N', 8: 'O'}
```
If you need to transmit this dictionary over an RPC mechanism, simply put, the struct and object RPC data structures are not designed to pair keys with values in
containers of an arbitrary size. Instead, they are designed to associate a small set of pre-defined attribute
names with the attribute values that they happen to carry for some particular object. If you try to use a
struct to pair random keys and values, you might inadvertently make it very difficult to use for people
unfortunate enough to be using statically-typed programming languages.
Instead, you should think of dictionaries being sent across RPCs as being like the `__dict__` attributes
of your Python objects, which you should generally
not find yourself using to associate an arbitrary set of keys with values.

All of this means that the dictionary that I showed a few moments ago should actually be serialized
as a list of explicitly labelled values if it is going to be used by a general-purpose RPC mechanism:
```python
{{'number': 1, 'symbol': 'H'},
{'number': 2, 'symbol': 'He'},
{'number': 3, 'symbol': 'Li'},
{'number': 4, 'symbol': 'Be'},
{'number': 5, 'symbol': 'B'},
{'number': 6, 'symbol': 'C'},
{'number': 7, 'symbol': 'N'},
{'number': 8, 'symbol': 'O'}}
```
Note that the preceding examples show the Python dictionary as you will pass it into your RPC call,
not the way it would be represented on the wire.

If you have a Python dictionary like the one we are discussing here, you can turn it into an RPCappropriate
data structure, and then change it back with code like this:
```python
>>> elements = {1: 'H', 2: 'He'}
>>> t = [ {'number': key, 'symbol': elements[key]} for key in elements ]
>>> t
[{'symbol': 'H', 'number': 1}, {'symbol': 'He', 'number': 2}]
>>> dict( (obj['number'], obj['symbol']) for obj in t )
{1: 'H', 2: 'He'}
```
Using named tuples might be an even better
way to marshal such values before sending them if you find yourself creating and destroying too many
dictionaries to make this transformation appealing.
