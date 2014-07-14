## Pure-Python Web Servers

A fun way to demonstrate that Python comes with “batteries included” is to enter a directory on your
system and run the [SimpleHTTPServer](https://docs.python.org/2/library/simplehttpserver.html?highlight=simplehttpserver#SimpleHTTPServer) Standard Library module as a stand-alone program:
```
root@erlerobot:~/Python_files# python -m SimpleHTTPServer
Serving HTTP on 0.0.0.0 port 8000 ...
```

If you direct your browser to localhost:8000, you will see the contents of this script's current
directory displayed for browsing, such as the listings provided by Apache when a site leaves a directory
browsable. Documents and images will load in your web browser when selected, based on the content
types chosen through the best guesses of the [mimetypes](https://docs.python.org/2/library/mimetypes.html?highlight=mimetypes#mimetypes) Standard Library module.
The `mimetypes` module converts between a filename or URL and the MIME type associated with the filename extension. Conversions are provided from filename to MIME type and from MIME type to filename extension; encodings are not supported for the latter conversion.

Yoday, we use
namespaces, callables, and duck-typed objects to provide much cleaner forms of extensibility. For
example, today an object like `start_response` is provided as an argument (dependency injection), and
the WSGI standard specifies its behavior rather than its inheritance tree (duck typing).The Standard
Library includes two other HTTP servers:

- [CGIHTTPServer](https://docs.python.org/2/library/cgihttpserver.html?highlight=cgihttpserver#CGIHTTPServer) takes the [SimpleHTTPServer](https://docs.python.org/2/library/simplehttpserver.html?highlight=simplehttpserver#SimpleHTTPServer) and, instead of just serving static files
off of the disk, it adds the ability to run CGI scripts .


- [SimpleXMLRPCServer](https://docs.python.org/2/library/simplexmlrpcserver.html?highlight=simplexmlrpcserver#SimpleXMLRPCServer) and [DocXMLRPCServer](https://docs.python.org/2/library/docxmlrpcserver.html?highlight=docxmlrpcserver#DocXMLRPCServer) each provide a server endpoint against
which client programs can make XML-RPC remote procedure calls. This protocol uses XML files
submitted through HTTP requests.

Note that none of the preceding servers is typically intended for production use; instead, they are
useful for small internal tasks for which you just need a quick HTTP endpoint to be used by other
services internal to a system or subnet. And while most Python web frameworks will provide a way to run
your application from the command line for debugging.
These pure-Python web servers can be very useful if you are writing an application that users will be
installing locally, and you want to provide a web interface without having to ship a separate web server
like Apache or nginx.
