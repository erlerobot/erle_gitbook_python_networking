## Instrumenting urllib2

We now turn to the HTTP protocol itself. Although its on-the-wire appearance is usually an internal
detail handled by web browsers and libraries like [urllib2 module](https://docs.python.org/2/library/urllib2.html?highlight=urllib2#urllib2).The urllib2 module defines functions and classes which help in opening URLs (mostly HTTP) in a complex world — basic and digest authentication, redirections, cookies and more.

we are going to adjust its behavior so that we
can see the protocol printed to the screen. Take a look at `verbose_handler.py`:
```

import StringIO, httplib, urllib2

class VerboseHTTPResponse(httplib.HTTPResponse):
    def _read_status(self):
        s = self.fp.read()
        print '-' * 20, 'Response', '-' * 20
        print s.split('\r\n\r\n')[0]
        self.fp = StringIO.StringIO(s)
        return httplib.HTTPResponse._read_status(self)

class VerboseHTTPConnection(httplib.HTTPConnection):
    response_class = VerboseHTTPResponse
    def send(self, s):
        print '-' * 50
        print s.strip()
        httplib.HTTPConnection.send(self, s)

class VerboseHTTPHandler(urllib2.HTTPHandler):
    def http_open(self, req):
        return self.do_open(VerboseHTTPConnection, req)
        ```

This customization prints out both the outgoing request
and the incoming response instead of keeping them both hidden.
