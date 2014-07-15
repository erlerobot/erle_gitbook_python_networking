## WSGI Middleware

Standard interfaces like WSGI make it possible for developers to create wrappers—a design-patterns
person would call these adapters—that accept a request from a server; modify, adjust, or record the
request; and then call a normal WSGI application with the modified environment. Such middleware can
also inspect and adjust the outgoing data stream; everything, in fact, is up for grabs, and essential
arbitrary changes can be made both to the circumstances under which a WSGI application runs, as well
as to the content that it returns.

- If several WSGI applications need to live at a single web site under different URLs,
then a piece of middleware can be given the URLs.
(you can read more in http://pythonpaste.org/)


- If each WSGI application on a web site were to keep its own list of passwords and
honor only its own session cookies, then users would have to log in again each
time they crossed an application boundary. By delegating authentication to WSGI
middleware, applications can be relieved even of the duty to provide their own
login page; instead, the middleware asks a user who lacks a session cookie to log
in; once a user is authenticated, the middleware can pass along the user's identity
to the applications by putting the user's information in the `environ` argument.
Both `repoze.who` and `repoze.what` can help site integrators assert site-wide
control over users and their permissions.


- Theming can be a problem when several small applications are combined to form
a larger web site. This is because each application typically has its own approach
to theming.This has led to the development of two competing tools,
[xdv](https://pypi.python.org/pypi/xdv/0.4b3) and [Deliverance](https://pypi.python.org/pypi/Deliverance/0.6.1), that let you build a single HTML theme and then provide
simple rules that pull text out of your back-end applications and drop it into your
theme in the right places.


- Debuggers can be created that call a WSGI application and, if an uncaught Python
exception is raised, display an annotated traceback to support debugging.
[WebError](https://pypi.python.org/pypi/WebError/0.10.3) actually provides the developer with a live, in-browser Python
command line prompt for every level in a stack trace at which the developer can
investigate a failure. Another popular tool is [repoze.profile](https://pypi.python.org/pypi/repoze.profile/2.0), which watches the
application as it processes requests and produces a report on which functions are
consuming the most CPU cycles.

If you are interested in what WSGI middleware is available, then you can visit this pair of sites to
learn more:
- http://wsgi.org/wsgi/Middleware_and_Utilities
- http://repoze.org/repoze_components.html#middleware

Today there are at least three major competing approaches in the Python community for crafting
modular components that can be used to build web sites:


- The WSGI middleware approach thinks that code reuse can often best be achieved
through a component stack, where each component uses WSGI to speak to the
next. Here, all interaction has to somehow be made to fit the model of a dictionary
of strings being handed down and then content being passed back up.


- Everything built atop the [Zope Toolkit](https://pypi.python.org/pypi/zope.browser/2.0.2) uses formal Design Pattern concepts like
interfaces and factories to let components discover one another and be configured
for operation. Thanks to adapters, components can often be used with widgets
that were not originally designed with a given type of component in mind.


- Several web frameworks have tried to adopt conventions that would make it easy
for third-party pieces of functionality to be added to an application easily. The
[Django](https://pypi.python.org/pypi/Django/1.6.5) community seems to have traveled the farthest in this direction, but it also
looks as though it has encountered quite serious roadblocks in cases where a
component needs to add its own tables to the database that have foreign-key
relationships with user tables.

These examples illustrate an important fact: WSGI middleware is a good idea that has worked very
well for a small class of problems where the idea of wrapping an application with concentric
functionality makes solid sense. However, most web programmers seem to want to use more typical
Python mechanisms like APIs, classes, and objects to combine their own code with existing components.
