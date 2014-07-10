## URL Anatomy

Uniform Resource Locators (URLs), are strings that tell your web browser how to fetch
resources from the World Wide WebThey are a subclass of the full set of possible Uniform Resource
Identifiers (URIs); specifically, they are URIs constructed so that they give instructions for fetching a
document, instead of serving only as an identifier.

To understand how they work,F consider a very simple URL, for example, like the following:` http://python.org`
If submitted to a web browser, this URL is interpreted as an order to resolve the host name
python.org to an IP address , make a TCP connection to that IP address at the standard
HTTP port 80 , and then ask for the root document / that lives at that site.

Now imagine another more complicated URL, imagine that we wanted the logo for Nord/LB, a large German bank. The resulting
URL might look something like this: `http://example.com:8080/Nord%2FLB/logo?shape=square&dpi=96`

Here, the URL specifies more information than our previous example did:
- The protocol will, again, be HTTP.
- The hostname example.com will be resolved to an IP.
- This time, port 8080 will be used instead of 80.
- Once a connection is complete, the remote server will be asked for the resource
named:
/Nord%2FLB/logo?shape=square&dpi=96

Web servers, in practice, have absolute freedom to interpret URLs as they please; however, the
intention of the standard is that this URL be parsed into two question-mark-delimited pieces. The first is
a path consisting of two elements:
- A Nord/LB path element.
- A logo path element.

The string following the ? is interpreted as a query containing two terms:

- A shape parameter whose value is square.
- A dpi parameter whose value is 96.

Any characters beyond the alphanumerics, a few punctuation marks—specifically the set $-
_.+!*'(),—and the special delimiter characters themselves (like the slashes) must be percent-encoded
by following a percent sign % with the two-digit hexadecimal code for the character.

You should
note that the following URL paths are not equivalent:
```
Nord%2FLB%2Flogo = A single path component, named Nord/LB/logo.

Nord%2FLB/logo = Two path components, Nord/LB and logo.

Nord/LB/logo= Three separate path components Nord, LB, and logo.
```

The most important Python routines for working with URLs live, appropriately enough, in their own
module.The [urlparse module](https://docs.python.org/2/library/urlparse.html?highlight=urlparse#urlparse); this module defines a standard interface to break URL strings up in components (addressing scheme, network location, path etc.), to combine the components back into a URL string, and to convert a “relative URL” to an absolute URL given a “base URL.”

```python
>>> from urlparse import urlparse, urldefrag, parse_qs, parse_qsl

With these routines, you can get large and complex URLs like the example given earlier and turn
them into their component parts, with RFC-compliant parsing already implemented for you:

```python
>>> p = urlparse('http://example.com:8080/Nord%2FLB/logo?shape=square&dpi=96')
>>> p
ParseResult(scheme='http', netloc='example.com:8080', path='/Nord%2FLB/logo',
» » » params='', query='shape=square&dpi=96', fragment='')
```
The query string that is offered by the ParseResult can then be submitted to one of the parsing
routines if you want to interpret it as a series of key-value pairs, which is a standard way for web forms to
submit them:

```python
>>> parse_qs(p.query)
{'shape': ['square'], 'dpi': ['96']}
```
Note that each value in this dictionary is a list, rather than simply a string. This is to support the fact
that a given parameter might be specified several times in a single URL; in such cases, the values are
simply appended to the list:

```python
>>> parse_qs('mode=topographic&pin=Boston&pin=San%20Francisco')
{'mode': ['topographic'], 'pin': ['Boston', 'San Francisco']}
```

This, you will note, preserves the order in which values arrive; of course, this does not preserve the
order of the parameters themselves because dictionary keys do not remember any particular order. If the
order is important to you, then use the `parse_qsl()` function instead (the l must stand for “list”):
```
>>> parse_qsl('mode=topographic&pin=Boston&pin=San%20Francisco')
[('mode', 'topographic'), ('pin', 'Boston'), ('pin', 'San Francisco')]
````

Finally, note that an “anchor” appended to a URL after a # character is not relevant to the HTTP
protocol. This is because any anchor is stripped off and is not turned into part of the HTTP request.
Instead, the anchor tells a web client to jump to some particular section of a document after the HTTP
transaction is complete and the document has been downloaded. To remove the anchor, use
`urldefrag()`:
```python
>>> u = 'http://docs.python.org/library/urlparse.html#urlparse.urldefrag'
>>> urldefrag(u)
('http://docs.python.org/library/urlparse.html', 'urlparse.urldefrag')
```

You can turn a ParseResult back into a URL by calling its `geturl()` method. When combined with
the `urlencode()` function, which knows how to build query strings, this can be used to construct new
URLs:
```python
>>> import urllib, urlparse
>>> query = urllib.urlencode({'company': 'Nord/LB', 'report': 'sales'})
>>> p = urlparse.ParseResult(
... 'https', 'example.com', 'data', None, query, None)
>>> p.geturl()
'https://example.com/data?report=sales&company=Nord%2FLB'
```
