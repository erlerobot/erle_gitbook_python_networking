## HTTP Session Hijacking

A perpetual problem with cookies is that web site designers do not seem to realize that cookies need to
be protected as zealously as your username and password. While it is true that well-designed cookies
expire and will no longer be accepted as valid by the server, cookies—while they last—give exactly as
much access to a web site as a username and password.


Some sites do not protect cookies at all: they might require HTTPS for your username and password,
but then return you to normal HTTP for the rest of your session.
Other sites are smart enough to protect subsequent page loads with HTTPS, even after you have left
the login page, but they forget that static data from the same domain, like images, decorations, CSS files,
and JavaScript source code, will also carry your cookie. The better alternatives are to either send all of
that information over HTTPS, or to carefully serve it from a different domain or path that is outside the
jurisdiction of the session cookie.



Should you happen to observe or capture a Cookie: header from an HTTP request that you observe,
remember that there is no need to store it in a CookieJar or represent it as a cookielib object at all.
Indeed, you could not do that anyway because the outgoing Cookie: header does not reveal the domain
and path rules that the cookie was stored with. Instead, just inject the Cookie: header raw into the
requests you make to the web site:
```python
request = urllib2.Request(url)
request.add_header('Cookie', intercepted_value)
info = urllib2.urlopen(request)
``
