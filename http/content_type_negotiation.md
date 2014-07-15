## Content Type Negotiation

It is always possible to simply make an HTTP request and let the server return a document with whatever
Content-Type: is appropriate for the information we have requested. Some of the usual content types
encountered by a browser include the following:
*text/html,
text/plain,
text/css,
image/gif,
image/jpeg,
image/x-png,
application/javascript,
application/pdf,
application/zip*.

If the web service is returning a generic data stream of bytes that it cannot describe more
specifically, it can always fall back to the content type:*application/octet-stream*.

The four headers that will interest you include the following:
*Accept,
Accept-Charset,
Accept-Language,
Accept-Encoding*

Each of these headers supports a comma-separated list of items, where each item can be given a
weight between one and zero (larger weights indicate more preferred items) by adding a suffix that
consists of a semi-colon and q= string to the item. The result will look something like this (using, for
illustration, the Accept: header that my Google Chrome browser seems to be currently using):
`
Accept: application/xml,application/xhtml+xml,text/html;q=0.9,text/plain;
» q=0.8,image/png,*/*;q=0.5`

This indicates that Chrome prefers XML and XHTML, but will accept HTML or even plain text if
those are the only document formats available; that Chrome prefers PNG images when it can get them;
and that it has no preference between all of the other content types in existence.
