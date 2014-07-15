## Memcached and Sharding

The design of Memcached illustrates an important principle that is used in several other kinds of
databases, and which you might want to employ in architectures of your own: the clients shard the
database by hashing the keys’ string values and letting the hash determine which member of the cluster
is consulted for each key.

To understand why this is effective, consider a particular key/value pair—like the key sq:42 and the
value 1764 that might be stored by `squares.py`. To make the best use of the RAM it has available, the
Memcached cluster wants to store this key and value exactly once. But to make the service fast, it wants
to avoid duplication without requiring any coordination between the different servers or
communication between all of the clients.

This means that all of the clients, without any other information to go on than (a) the key and (b) the
list of Memcached servers with which they are configured, need some scheme for working out where
that piece of information belongs. If they fail to make the same decision, then not only might the key and
value be copied on to several servers and reduce the overall memory available, but also a client’s attempt
to remove an invalid entry could leave other invalid copies elsewhere.

The solution is that the clients all implement a single, stable algorithm that can turn a key into an
integer n that selects one of the servers from their list. They do this by using a “hash” algorithm, which
mixes the bits of a string when forming a number so that any pattern in the string is, hopefully,
obliterated. You can find [hashlib module](https://docs.python.org/2/library/hashlib.html?highlight=hashlib) in the Python Standard Library.

To see why patterns in key values must be obliterated, consider ` hashing.py`. It loads a dictionary of
English words (you might have to download a dictionary of your own or adjust the path to make the
script run on your own machine), and explores how those words would be distributed across four
servers if they were used as keys. The first algorithm tries to divide the alphabet into four roughly equal
sections and distributes the keys using their first letter; the other two algorithms use hash functions.
```python

import hashlib

def alpha_shard(word):
    """Do a poor job of assigning data to servers by using first letters."""
    if word[0] in 'abcdef':
        return 'server0'
    elif word[0] in 'ghijklm':
        return 'server1'
    elif word[0] in 'nopqrs':
        return 'server2'
    else:
        return 'server3'

def hash_shard(word):
    """Do a great job of assigning data to servers using a hash value."""
    return 'server%d' % (hash(word) % 4)

def md5_shard(word):
    """Do a great job of assigning data to servers using a hash value."""
    # digest() is a byte string, so we ord() its last character
    return 'server%d' % (ord(hashlib.md5(word).digest()[-1]) % 4)

words = open('/usr/share/dict/words').read().split()

for function in alpha_shard, hash_shard, md5_shard:
    d = {'server0': 0, 'server1': 0, 'server2': 0, 'server3': 0}
    for word in words:
        d[function(word.lower())] += 1
    print function.__name__[:-6], d
    ```

The `hash()` function is Python’s own built-in hash routine, which is designed to be blazingly fast
because it is used internally to implement Python dictionary lookup.
