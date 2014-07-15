## WSGI


Integrating Python with web servers was much improved by the creation of PEP 333, which defines the Python Web Server
Gateway Interface (WSGI):
http://legacy.python.org/dev/peps/pep-0333/.

WSGI introduced a single calling convention that every web server could implement, thereby
making that web server instantly compatible with all of the Python web applications and web
frameworks that also support WSGI.

At the Python library you can get more information about the [wsgiref module](https://docs.python.org/2/library/wsgiref.html?highlight=wsgi#wsgiref).This module provides a variety of utility functions for working with WSGI environments.The `wsgiref` package, whose `simple_server` we will use in the example, also contains several utilities for
working with WSGI. It includes functions for examining, further unpacking, and modifying the environ
object; a prebuilt iterator for streaming large files back to the server; and even a validate sub-module
whose routines can check a WSGI application to see whether it complies with the specification when
presented with a series of representative requests.

Developers generally avoid writing raw. WSGI applications because the conveniences of even a
simple web framework make code so much easier to write and maintain. But, for the sake of illustration,
`wsgi_app.py` shows a small WSGI application whose front page asks the user to type a string. Submitting
the string takes the user to a second web page, where he can see its base64 encoding. From there, a link
will take him back to the first page to repeat the process.

```python
import cgi, base64
from wsgiref.simple_server import make_server

def page(content, *args):
    yield '<html><head><title>wsgi_app.py</title></head><body>'
    yield content % args
    yield '</body>'

def simple_app(environ, start_response):
    gohome = '<br><a href="/">Return to the home page</a>'
    q = cgi.parse_qs(environ['QUERY_STRING'])

    if environ['PATH_INFO'] == '/':

        if environ['REQUEST_METHOD'] != 'GET' or environ['QUERY_STRING']:
            start_response('400 Bad Request', [('Content-Type', 'text/plain')])
            return ['Error: the front page is not a form']

        start_response('200 OK', [('Content-Type', 'text/html')])
        return page('Welcome! Enter a string: <form action="encode">'
                    '<input name="mystring"><input type="submit"></form>')

    elif environ['PATH_INFO'] == '/encode':

        if environ['REQUEST_METHOD'] != 'GET':
            start_response('400 Bad Request', [('Content-Type', 'text/plain')])
            return ['Error: this form does not support POST parameters']

        if 'mystring' not in q or not q['mystring'][0]:
            start_response('400 Bad Request', [('Content-Type', 'text/plain')])
            return ['Error: this form requires a "mystring" parameter']

        my = q['mystring'][0]
        start_response('200 OK', [('Content-Type', 'text/html')])
        return page('<tt>%s</tt> base64 encoded is: <tt>%s</tt>' + gohome,
                    cgi.escape(repr(my)), cgi.escape(base64.b64encode(my)))

    else:
        start_response('404 Not Found', [('Content-Type', 'text/plain')])
        return ['That URL is not valid']

print 'Listening on localhost:8000'
make_server('localhost', 8000, simple_app).serve_forever()
```

import cgi, base64
from wsgiref.simple_server import make_server

def page(content, *args):
    yield '<html><head><title>wsgi_app.py</title></head><body>'
    yield content % args
    yield '</body>'

def simple_app(environ, start_response):
    gohome = '<br><a href="/">Return to the home page</a>'
    q = cgi.parse_qs(environ['QUERY_STRING'])

    if environ['PATH_INFO'] == '/':

        if environ['REQUEST_METHOD'] != 'GET' or environ['QUERY_STRING']:
            start_response('400 Bad Request', [('Content-Type', 'text/plain')])
            return ['Error: the front page is not a form']

        start_response('200 OK', [('Content-Type', 'text/html')])
        return page('Welcome! Enter a string: <form action="encode">'
                    '<input name="mystring"><input type="submit"></form>')

    elif environ['PATH_INFO'] == '/encode':

        if environ['REQUEST_METHOD'] != 'GET':
            start_response('400 Bad Request', [('Content-Type', 'text/plain')])
            return ['Error: this form does not support POST parameters']

        if 'mystring' not in q or not q['mystring'][0]:
            start_response('400 Bad Request', [('Content-Type', 'text/plain')])
            return ['Error: this form requires a "mystring" parameter']

        my = q['mystring'][0]
        start_response('200 OK', [('Content-Type', 'text/html')])
        return page('<tt>%s</tt> base64 encoded is: <tt>%s</tt>' + gohome,
                    cgi.escape(repr(my)), cgi.escape(base64.b64encode(my)))

    else:
        start_response('404 Not Found', [('Content-Type', 'text/plain')])
        return ['That URL is not valid']

The first thing to note in this code listing is that two very different objects are being created: a WSGI
server that knows how to use HTTP to talk to a web browser and an application written to respond
correctly when invoked per the WSGI calling convention. Note that these two pieces—the client and
server—could easily be swapped out.
This code example should make the calling convention clear enough:

- For each incoming request, the application is called with an environ object,
giving it the details of the HTTP request and a live, callable, and named
`start_response()`.


- Once the application has decided what HTTP response code and headers need
to be returned, it makes a single call to `start_response()`. Its headers will be
combined with any headers that the WSGI server might already provide to the
client.


- Finally, the application needs only to return the actual content—either a list of
strings or a generator yielding strings. Either way, the strings will be
concatenated by the WSGI server to produce the response body that is
transmitted back to the client. Generators are useful for cases where it would be
unwise for an application to try loading all of the content (like large files) into
memory at once.
