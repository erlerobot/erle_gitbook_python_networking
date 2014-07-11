## Choosing a Web Server

All of the popular open source web servers can be used to serve Python web applications, so the full
range of modern options is available:
- *Apache HTTP Server*: Since taking the lead as the most popular HTTP server
back in 1996. Its stated goal is flexibility and modularity; it is
reasonably fast, but it will not win speed records against more recent servers
that focus only on speed. Its configuration files can be a bit long and verbose,
but through them Apache offers very powerful options for applying different
rules and behaviors to different directories and URLs. A variety of extension
modules are available (many of which come bundled with it), and user
directories can have separate `.htaccess `configuration files that make further
adjustments to the main configuration.

- *nginx (“engine X”)*: The
nginx server has become a great favorite of organizations with a large volume of
content that needs to be served quickly. It is considered fairly easy to configure.
lighttpd (“lighty”): First written to demonstrate an architecture that could
support tens of thousands of open client sockets (both nginx and Cherokee are
also contenders in this class), this server is known for being very easy to
configure. Some system administrators complain about its memory usage, but
many others have observed no problems with it.

- *Cherokee*: Not only does this server offer performance that might edge out even
nginx and lighttpd, but it lets you configure the server through a built-in web
interface.

So tocombine each of these servers with Python; for example in the case of Apache:the [mod_wsgi module](https://docs.python.org/2/library/multiprocessing.html?highlight=mod_wsgi) has a daemon mode where it internally runs your Python code
inside a stack of dedicated server processes that are separate from Apache. Each Web Server Gateway Interface(WSGI) process can even
run as a different user. If you really want to use Apache as your front end, this is one of the best options
available.

But the most strongly recommended approach today is to set up one of the three fast servers to
provide your static content, and then use one of the following three techniques to run your Python code
behind them:

- Use HTTP proxying so that your `nginx`, `lighttpd`, or Cherokee front-end server
delivers HTTP requests for dynamic web pages to a back-end Apache instance
running `mod_wsgi`.
- Use the FastCGI protocol or SCGI protocol to talk to a flup instance running your
Python code.
- Use the `uwsgi` protocol to talk to a uWSGI instance running your Python code.


At this point, you understand something of the larger context in which Python web applications are
usually run; you are now ready to turn your attention to the task of programming.
