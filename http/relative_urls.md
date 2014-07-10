## Relative URLs

Very often, the links used in web pages do not specify full URLs, but relative URLs that are missing
several of the usual components. When one of these links needs to be resolved, the client needs to fill in
the missing information with the corresponding fields from the URL used to fetch the page in the first
place.

The simplest relative links are the names of pages one level deeper than the base page:

```python
>>> urlparse.urljoin('http://www.python.org/psf/', 'grants')
'http://www.python.org/psf/grants'
>>> urlparse.urljoin('http://www.python.org/psf/', 'mission')
'http://www.python.org/psf/mission'
```
Note the crucial importance of the trailing slash in the URLs:
```python
>>> urlparse.urljoin('http://www.python.org/psf', 'grants')
'http://www.python.org/grants'
```

Like file system paths on the POSIX and Windows operating systems, . can be used for the current
directory and .. is the name of the parent:

```python
>>> urlparse.urljoin('http://www.python.org/psf/', './mission')
'http://www.python.org/psf/mission'
>>> urlparse.urljoin('http://www.python.org/psf/', '../news/')
'http://www.python.org/news/'
>>> urlparse.urljoin('http://www.python.org/psf/', '/dev/')
'http://www.python.org/dev'
````

And, as illustrated in the last example, a relative URL that starts with a slash is assumed to live at the
top level of the same site as the original URL.
Happily, the `urljoin()` function ignores the base URL entirely if the second argument also happens
to be an absolute URL. This means that you can simply pass every URL on a given web page to the
`urljoin()` function, and any relative links will be converted; at the same time, absolute links will be
passed through untouched:
```python
>>> # Absolute links are safe from change
...
>>> urlparse.urljoin('http://www.python.org/psf/', 'http://yelp.com/')
'http://yelp.com/'
```
