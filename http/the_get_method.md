## The GET Method and The Host Header

When the earliest version of HTTP was first invented, it had a single power: to issue a method called GET
that named and returned a hypertext document from a remote server. That method is still the backbone
of the protocol today.

The GET method, like all HTTP methods, is the first thing transmitted as part of an HTTP request,
and it is immediately followed by the request headers. For simple GET methods, the request simply ends
with the blank line that terminates the headers so the server can immediately stop reading and send a
response.
```python
>>> info = opener.open('http://www.ietf.org/rfc/rfc2616.txt')
GET /rfc/rfc2616.txt HTTP/1.1
Accept-Encoding: identity
Host: www.ietf.org
Connection: close...
```
The opener’s `open()` method, like the plain `urlopen()` function at the top level of urllib2, returns an
information object that lets us examine the result of the GET method. You can see that the HTTP request
started with a status line containing the HTTP version, a status code, and a short message. The info
object makes these available as object attributes; it also lets us examine the headers through a
dictionary-like object:
```python
>>> info.code
200
>>> info.msg
'OK'
>>> sorted(info.headers.keys())
['accept-ranges', 'connection', 'content-length', 'content-type',
'date', 'etag', 'last-modified', 'server', 'vary']
>>> info.headers['Content-Type']
'text/plain'
```
Finally, the info object is also prepared to act as a file. The HTTP response status line, the headers,
and the blank line that follows them have all been read from the HTTP socket, and now the actual
document is waiting to be read. As is usually the case with file objects, you can either start reading the
info object in pieces through read(N) or readline(); or you can choose to bring the entire data stream
into memory as a single string:
```python
>>> print info.read().strip()
Network Working Group R. Fielding
Request for Comments: 2616 UC Irvine
Obsoletes: 2068 J. Gettys
Category: Standards Track Compaq/W3C
...
```
These are the first lines of the longer text file that you will see if you point your web browser at the
same URL.


In a world of six billion people and four billion IP addresses, the need quickly became clear to
support servers that might host dozens of web sites at the same IP.
And that is why the URL location is now included in every HTTP request. For compatibility, it has
not been made part of the GET request line itself, but has instead been stuck into the headers under the
name Host.
```python
>>> info = opener.open('http://www.google.com/')
-------------------- Response --------------------
HTTP/1.1 302 Found
Cache-Control: private
...
--------------------------------------------------
GET /?gfe_rd=cr&ei=OY6_U_qjHOeA8QeTg4H4BQ HTTP/1.1
Accept-Encoding: identity
Host: www.google.es
Connection: close
User-Agent: Python-urllib/2.7
-------------------- Response --------------------
HTTP/1.1 200 OK
...
```


Depending on how they are configured, servers might return entirely different sites when
confronted with two different values for Host; they might present slightly different versions of the same
site; or they might ignore the header altogether. But semantically, two requests with different values for
Host are asking about two entirely different URLs.
When several sites are hosted at a single IP address, those sites are each said to be served by a
virtual host, and the whole practice is sometimes referred to as virtual hosting.


Is also important to take care that when handling HTTP diffrent responses can happend, between them codes, errors, and redirection. You can read more about this [here](https://support.google.com/webmasters/answer/40132?hl=en).
