## The GET Method and The Host Header

When the earliest version of HTTP was first invented, it had a single power: to issue a method called GET
that named and returned a hypertext document from a remote server. That method is still the backbone
of the protocol today.

The GET method, like all HTTP methods, is the first thing transmitted as part of an HTTP request,
and it is immediately followed by the request headers. For simple GET methods, the request simply ends
with the blank line that terminates the headers so the server can immediately stop reading and send a
response.

In a world of six billion people and four billion IP addresses, the need quickly became clear to
support servers that might host dozens of web sites at the same IP.
And that is why the URL location is now included in every HTTP request. For compatibility, it has
not been made part of the GET request line itself, but has instead been stuck into the headers under the
name Host.

Depending on how they are configured, servers might return entirely different sites when
confronted with two different values for Host; they might present slightly different versions of the same
site; or they might ignore the header altogether. But semantically, two requests with different values for
Host are asking about two entirely different URLs.
When several sites are hosted at a single IP address, those sites are each said to be served by a
virtual host, and the whole practice is sometimes referred to as virtual hosting.


Is also important to take care that when handling HTTP diffrent responses can happend, between them codes, errors, and redirection. You can read more about this [here](https://support.google.com/webmasters/answer/40132?hl=en)
