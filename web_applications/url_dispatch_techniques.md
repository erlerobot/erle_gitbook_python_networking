## URL Dispatch Techniques

The various Python web frameworks tend to handle URL dispatch quite differently.

- Some small frameworks like [Bottle](https://pypi.python.org/pypi/bottle/0.12.7) and [Flask](https://pypi.python.org/pypi/Flask/0.10.1) let you create small applications by
decorating a series of callables with URL patterns; small applications can then be
combined later by placing them beneath one or more top-level applications.


- Others frameworks, like [Django](https://pypi.python.org/pypi/Django/1.6.5), [Pylons](https://pypi.python.org/pypi/Pylons/1.0.1), and [Werkzeug](https://pypi.python.org/pypi/Werkzeug/0.9.6), encourage each
application to define its URLs all in one place. This breaks your code into two
levels, where URL dispatch happens in one location and rendering in another.
This separation makes it easier to review all of the URLs that an application
supports; it also means that you can attach code to new URLs without having to
modify the functions themselves.


- Another approach has you define controllers, which are classes that represent
some point in the URL hierarchy—say, the path /cart—and then write methods
on the controller class named `view()` and `edit()` if you want to support sub-pages
named /cart/view and /cart/edit. [CherryPy](https://pypi.python.org/pypi/CherryPy/3.5.0), [TurboGears2](https://pypi.python.org/pypi/TurboGears2/2.3.3), and [Pylons](https://pypi.python.org/pypi/Pylons/1.0.1) (if you use
controllers instead of Routes) all support this approach. While determining later
what URLs are supported can mean traversing a maze of different connected
classes, this approach does allow for dynamic, recursive URL spaces that exist only
at runtime as classes hand off dispatch requests based on live data about the site
structure.


- A large community with its own conferences exists around the [Zope](https://pypi.python.org/pypi/zope.browser/2.0.2) framework.


The various mechanisms for URL dispatch can all be used to produce fairly clean design, and
choosing from among them is largely a matter of taste.


