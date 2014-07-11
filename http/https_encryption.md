## HTTPS Encryption

An encrypted URL starts with https: instead of simply http:, uses the default port 443 instead of
port 80, and uses TLS.

Encryption has to be negotiated before the user can
send his HTTP request, lest all of the information in it be divulged; but until the request is transmitted,
the server does not know what Host: the request will specify. Therefore, encrypted web sites still live
under the old problem of having to use a different IP address for every domain that must be hosted.

A technique known as “Server Name Indication” (SNI) has been developed to get around this
traditional restriction; however, Python does not yet support it. It appears, though, that a patch was
applied to the Python 3 trunk with this feature, only days prior to the time of writing. Here is the ticket in
case you want to follow the issue: http://bugs.python.org/issue5639.

To use HTTPS from Python, simply supply an https: method in your URL:
```python
>>> info = urllib2.urlopen('https://www.ietf.org/rfc/rfc2616.txt')
>>>
```

If the connection works properly, then neither your government nor any of the various large and
shadowy corporations that track such things should be able to easily determine either the search term
you used or the results you viewed.
