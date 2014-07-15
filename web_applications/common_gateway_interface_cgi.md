## Common Gateway Interface (CGI)

When the first experiments were taking place with dynamically generated web pages,a calling convention was necessary, and so the Common Gateway Interface (CGI) was
defined. It allowed programs in all sorts of languages—C, the various Unix shells, awk, Perl, Python, PHP,
and so forth—to be partners in generating dynamic content.

Today, the design of CGI is considered something of a disaster. Running a new process from scratch
is just about the most expensive single operation that you can perform on a modern operating system,
and requiring that this take place for every single incoming HTTP request is simply madness. You should
avoid CGI under all circumstances. But it is possible you might someday have to connect Python code to
a legacy HTTP server that does not support at least FastCGI or SCGI, so I will outline CGI's essential
features.
Three standard lines of communication that already existed between parent and child processes on
Unix systems were used by web servers when invoking a CGI script:

- The Unix environment—a list of strings provided to each process upon its
invocation that traditionally includes things like TZ=EST (the time zone) and
COLUMNS=80 (user's screen width)—was instead stuffed full of information about
the HTTP request that the CGI script was being called upon to answer. The various
parts of the request's URL; the user agent string; basic information about the web
server; and even a cookie could be included in the list of colon-separated keyvalue
pairs.


- The standard input to the script could be read to end-of-file to receive whatever
data had been submitted in the body of the HTTP request using POST. Whether a
request was indeed a POST could be checked by examining the REQUEST_METHOD
environment variable.

- Finally, the script would produce content, which it did by writing HTTP headers, a
blank line, and then a response body to its standard output. To be a valid
response, a Content-Type header was generally necessary at a minimum—though
in its absence, some web servers would instead accept a Location header as a
signal that they should send a redirect.

Should you ever need to run Python behind an HTTP server that only supports CGI, then I
recommend that you use the [CGIHandler](https://docs.python.org/2/library/wsgiref.html?highlight=cgihandler#wsgiref.handlers.CGIHandler) module from the `wsgiref` Standard Library package(This is useful when you have a WSGI application and want to run it as a CGI script). This lets
you use a normal Python web framework to write your service—or, alternatively, to roll up your sleeves
and write a raw WSGI application—and then offer the HTTP server a CGI script, as shown here:
```python
import CGIHandler, MyWSGIApp
my_wsgi_app = MyWSGIApp() # configuration necessary here?
CGIHandler().run(my_wsgi_app)
```

Be sure to check whether your web framework of choice already provides a way to invoke it as a CGI
script; if so, your web framework will already know all of the steps involved in loading and configuring
your application.
