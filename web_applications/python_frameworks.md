## Python Web Frameworks

Now we are going to talk about an entirely different discipline: web application development.

Network programmers think about things like sockets, port numbers, protocols, packet loss, latency,
framing, and encodings. Although all of these concepts must also be in the back of a web developer's
mind, her actual attention is focused on a set of technologies so intricate and fast-changing that the
actual packets and latencies are recalled to mind only when they are causing trouble. The web developer
needs to think instead about HTML, GET, POST, forms, REST, CSS, JavaScript, Ajax, APIs, sprites,
compression, and emerging technologies like HTML5 and WebSocket. The web site exists in her mind
primarily as a series of documents that users will traverse to accomplish goals.

Web frameworks exist to help programmers step back from the details of HTTP—which is, after all,
an implementation detail most users never even become aware of—and to write code that focuses on
the nouns of web design. `wsgi_app.py` shows how even a very modest Python microframework can be
used to reorient the attention of a web programmer.

You can install the framework [bottle](https://pypi.python.org/pypi/bottle/0.12.7) and run the listing
once you have activated a virtual environment, like this:

The `bottle_app.py`:
```
import base64, bottle
bottle.debug(True)
app = bottle.Bottle()

@app.route('/encode')
@bottle.view('bottle_template.html')
def encode():
    mystring = bottle.request.GET.get('mystring')
    if mystring is None:
        bottle.abort(400, 'This form requires a "mystring" parameter')
    return dict(mystring=mystring, myb=base64.b64encode(mystring))

@app.route('/')
@bottle.view('bottle_template.html')
def index():
    return dict(mystring=None)

bottle.run(app=app, host='localhost', port=8080)
```
```
root@erlerobot:~/Python_files# pip install bottle
root@erlerobot:~/Python_files# python bottle_app.py
```

The `wsgi_app.py`:

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

print 'Listening on localhost:8000'
make_server('localhost', 8000, simple_app).serve_forever()
```
In `bottle_app.py` the attention was on the single incoming HTTP request, and the branches in our
logic explored all of the possible lifespans for that particular protocol request. `wsgi_app.py` changes the
focus to the pages that actually exist on the site and giving each of these pages reasonable behaviors. The
same tree of possibilities exists, but the tree exists implicitly thanks to the possible URLs defined in the
code, not because the programmer has written a large if statement.

```
%# The page template that goes with bottle_app.py.
%#
<html><head><title>bottle_app.py</title></head>
<body>
  %if mystring is None:
    Welcome! Enter a string:
    <form action="encode"><input name="mystring"><input type="submit"></form>
  %else:
    <tt>{{mystring}}</tt> base64 encoded is: <tt>{{myb}}</tt><br>
    <a href="/">Return to the home page</a>
  %end
</body>
```
It might seem merely a pleasant convenience that we can use the `Bottle SimpleTemplate to insert our
variables into a web page and know that they will be escaped correctly. But the truth is that templates
serve, just like schemes for URL dispatch, to re-orient our attention: instead of the resulting web page
existing in our minds as what will result when the strings in our program listing are finally concatenated,
we get to lay out its HTML intact, in order, and in a file that can actually take an .html extension and be highlighted and indented as HTML in our editor. The Python program will no longer impede our
relationship with our markup.

And full-fledged Python frameworks abstract away even more implementation details. A very
important feature they typically provide is data abstraction: instead of talking to a database using its raw
APIs, a programmer can define models, laying out the data fields so they are easy to instantiate, search,
and modify. And some frameworks can provide entire RESTful APIs that allow creation, inspection,
modification, and deletion with PUT, GET, POST, and DELETE. The programmer merely needs to define the
structure of his data document, and then name the URL at which the tree of REST objects should be
based.

When looking for a web framework, you will find that the various frameworks differ on a few major
points. The upcoming sections will walk you through what these points are, and how they might affect
your development experience.
