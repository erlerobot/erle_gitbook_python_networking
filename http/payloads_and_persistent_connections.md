## Payloads and Persistent Connections

By default, HTTP/1.1 servers will keep a TCP connection open even after they have delivered their
response. This enables you to make further requests on the same socket and avoid the expense of
creating a new socket for every piece of data you might need to download. Keep in mind that
downloading a modern web page can involve fetching dozens, if not hundreds, of separate pieces of
content.
The HTTPConnection class provided by urllib2 lets you take advantage of this feature. In fact, all
requests go through one of these objects; when you use a function like `urlopen()` or use the `open()`
method on an opener object, an HTTPConnection object is created behind the scenes, used for that one
request, and then discarded. When you might make several requests to the same site, use a persistent
connection instead:
```
>>> import httplib
>>> c = httplib.HTTPConnection('www.python.org')
>>> c.request('GET', '/')
>>> original_sock = c.sock
>>> content = c.getresponse().read() # get the whole page
>>> c.request('GET', '/about/')
>>> c.sock is original_sock
True
```
Now, if we insert this header manually, then we force the
HTTPConnection object to create a second socket when we ask it for a second page:
```
>>> c = httplib.HTTPConnection('www.python.org')
>>> c.request('GET', '/', headers={'Connection': 'close'})
>>> original_sock = c.sock
>>> content = c.getresponse().read()
>>> c.request('GET', '/about/')
>>> c.sock is original_sock
False
```

Note that HTTPConnection does not raise an exception when one socket closes and it has to create
another one; you can keep using the same object over and over again. This holds true regardless of
whether the server is accepting all of the requests over a single socket, or it is sometimes hanging up and
forcing HTTPConnection to reconnect.
