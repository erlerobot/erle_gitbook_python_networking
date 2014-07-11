## REST And More HTTP Methods

Web-based APIs, which fetch documents and data using GET and
POST to specific URLs. Therefore, we should immediately note that many modern web services try to
integrate their APIs more tightly with HTTP by going beyond the two most common HTTP methods by
implementing additional methods like PUT and DELETE.


A design pattern named “Representational State Transfer” has therefore been taking hold in many
developer communities. It specifies that the nouns of an API should live at their
own URLs. For example, PUT, GET, POST, and DELETE should be used, respectively, to create, fetch, modify,
and remove the documents living at these URLs.

By coupling this basic recommendation with further guidelines, the REST methodology guides the
creation of web services that make more complete use of the HTTP protocol. Such web services also offer quite clean semantics, and can be accelerated
by the same caching proxies that are often used to speed the delivery of normal web pages.

Note that HTTP supports arbitrary method names, even though the standard defines specific
semantics for GET and POST and all of the rest. Tradition would dictate using the well-known methods
defined in the standard unless you are using a specific framework or methodology that recognizes and
has defined other methods.
