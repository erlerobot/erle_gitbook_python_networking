## Compression

While many documents delivered over HTTP are already fairly heavily compressed, including images and file formats like PDF ,
web pages themselves are written in verbose SGML dialects that can consume much
less bandwidth if subjected to generic textual compression. Similarly, CSS and JavaScript files also
contain very stereotyped patterns of punctuation and repeated variable names, which is very amenable
to compression.

Web clients can make servers aware that they accept compressed documents by listing the formats
they support in a request header, as in this example:
`Accept-Encoding: gzip``

For some reason, many sites seem to not offer compression unless the User-Agent: header specifies
something they recognize. Thus, to convince Google to compress its Google News page, you have to use
`urllib2` something like this:
```python
>>> request = urllib2.Request('http://news.google.com/')
>>> request.add_header('Accept-Encoding', 'gzip')
>>> request.add_header('User-Agent', 'Mozilla/5.0')
>>> info = opener.open(request)
--------------------------------------------------
GET / HTTP/1.1
Host: news.google.com
User-Agent: Mozilla/5.0
Connection: close
Accept-Encoding: gzip
-------------------- Response --------------------
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
...
Content-Encoding: gzip
...
```

Remember that web servers do not have to perform compression, and that many will ignore your
`Accept-Encoding: header`. Therefore, you should always check the content encoding of the response, and
perform decompression only when the server declares that it is necessary:
```python
>>> info.headers['Content-Encoding'] == 'gzip'
True
>>> import gzip, StringIO
>>> gzip.GzipFile(fileobj=StringIO.StringIO(info.read())).read()
'<!DOCTYPE HTML ...<html>...</html>'
```

As you can see, Python does not let us pass the file-like info response object directly to the GzipFile
class because, it is not quite file-like enough. Here, we can
perform the quick work-around of reading the whole compressed file into memory and then wrapping it
in a StringIO object that does support `tell().
