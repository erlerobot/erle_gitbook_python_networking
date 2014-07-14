## Web Servers and Python

Acceptable web site performance generally requires the ability to serve several users concurrently.

To avoid corrupting in-memory data structures, C Python
employs a Global Interpreter Lock (GIL), so that only one thread in a multi-threaded program can
actually be executing Python code at any given time. Thus Python will let you create as many threads as
you want in a given process; however, only one thread can run code at a time, as though your threads
were confined to a single processor.

A typical web application receives and parses the
user's request, then makes a corresponding request to the database behind it; while that thread is
waiting for a response from the database, the GIL is available for any other threads that need to run
Python code. Finally the database answers; the waiting thread reacquires the GIL; and, in a quick blaze
of CPU activity, the data is turned into an attractive web page, and the response is sent winging its way
back to the user.

Thus threads can sometimes at least perform decently. Nevertheless, multiple processes are the
more general way to scale. This is because, as a service gets bigger, additional processes can be brought
up on additional machines, rather than being confined to a single machine. Threads, no matter their
other merits, cannot do that!
There are two general approaches to running a Python web application inside of a collector of
identical worker processes:

- The Apache web server can be combined with the popular `mod_wsgi` module to
host a separate Python interpreter in every Apache worker process.


- The web application can be run inside of either the flup server or the uWSGI server.
Both of these servers will manage a pool of worker processes where each process
hosts a Python interpreter running your application. The front-end web server can
submit requests to flup using either the standard Fast CGI (FCGI) or Simple CGI
(SCGI) protocol, while it has to speak to uWSGI in its own special “uwsgi” protocol
(whose name is all lowercase to distinguish it from the name of the server).
