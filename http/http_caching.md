## HTTP Caching

Many elements of a typical web site design are repeated on every page you visit, and your browsing
would slow to a crawl if every image and decoration had to be downloaded separately for every page you
viewed. Well-configured web servers therefore add headers to every HTTP response that allow browsers,
as well as any proxy caches between the browser and the server, to continue using a copy of a
downloaded resource for some period of time until it expires.

There are two basic mechanisms by which servers can support client caching.
In the first approach, an HTTP response includes an Expires: header that formats a date and time
using the same format as the standard Date: header:
`Expires: Sun, 21 Jan 2010 17:06:12 GMT`.
However, this requires the client to check its clock—and many computers run clocks that are far
ahead of or behind the real current date and time.

This brings us to a second, more modern alternative, the Cache-Control header, that depends only
on the client being able to correctly count seconds forward from the present. For example, to allow an
image or page to be cached for an hour but then insist that it be refetched once the hour is up, a cache
control header could be supplied like this:
`Cache-Control: max-age=3600, must-revalidate`.
