## The HEAD Method

It’s possible that you might want your program to check a series of links for validity or whether they have
moved, but you do not want to incur the expense of actually downloading the body that would follow the
HTTP headers. In this case, you can issue a HEAD request. This is directly possible through `httplib`, but it
can also be performed by `urllib2` if you are willing to write a small request class of your own:
```python
>>> class HeadRequest(urllib2.Request):
... def get_method(self):
... return 'HEAD'
...
>>> info = urllib2.urlopen(HeadRequest('http://www.google.com/'))
>>> info.read()
''
```
You can see that the body of the response is completely empty.
