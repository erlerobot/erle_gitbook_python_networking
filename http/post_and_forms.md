## POST And Forms

The POST HTTP method was designed to power web forms. When forms are used with the GET method,
which is indeed their default behavior, they append the form’s field values to the end of the URL:
`http://www.google.com/search?q=python+language`

The construction of such a URL creates a new named location that can be saved; bookmarked;
referenced from other web pages; and sent in e-mails, Tweets, and text messages. And for actions like
searching and selecting data, these features are perfect.
But what about a login form that accepts your e-mail address and password? Not only would there
be negative security implications to having these elements appended to the form URL—such as the fact
that they would be displayed on the screen in the URL bar and included in your browser history—but
surely it would be odd to think of your username and password as creating a new location or page on the
web site in question:
`
http://example.com/welcome?email=brandon@rhodesmill.org&pw=aaz9Gog3`

Building URLs in this way would imply that a different page exists on the example.com web site for
every possible password that you could try typing. This is undesirable for obvious reasons.
And so the POST method should always be used for forms that are not constructing the name of a
particular page or location on a web site, but are instead performing some action on behalf of the caller.
Forms in HTML can specify that they want the browser to use POST by specifying that method in their
`<form> element:
```
<form name="myloginform" action="/access/dummy" method="post">
E-mail: <input type="text" name="e-mail" size="20">
Password: <input type="password" name="password" size="20">
<input type="submit" name="submit" value="Login">
</form>
```

Instead of stuffing form parameters into the URL, a POST carries them in the body of the request. We
can perform the same action ourselves in Python by using `urlencode` to format the form parameters, and
then supplying them as a second parameter to any of the `urllib2` methods that open a URL. - (From the standard Python library:`urllib.urlencode(query[, doseq])`
Convert a mapping object or a sequence of two-element tuples to a “percent-encoded” string, suitable to pass to `urlopen()` above as the optional data argument. This is useful to pass a dictionary of form fields to a POST request. )

```python
form = urllib.urlencode({'inputstring': 'Atlanta, GA'})
>>> response = opener.open('http://forecast.weather.gov/zipcity.php', form)
--------------------------------------------------
POST /zipcity.php HTTP/1.1
...
Content-Length: 25
Host: forecast.weather.gov
Content-Type: application/x-www-form-urlencoded
...
--------------------------------------------------
inputstring=Atlanta%2C+GA
-------------------- Response --------------------
HTTP/1.1 302 Found
...
Location: http://forecast.weather.gov/MapClick.php?CityName=Atlanta&state=GA􀀁
&site=FFC&textField1=33.7629&textField2=-84.4226&e=1
...
--------------------------------------------------
GET /MapClick.php?CityName=Atlanta&state=GA&site=FFC&textField1=33.7629&textField2=􀀁
-84.4226&e=1 HTTP/1.1
...
-------------------- Response --------------------
HTTP/1.1 200 OK
...
```

Although our opener object is putting a dashed line between each HTTP request and its payload for
clarity (a blank line, you will recall, is what really separates headers and payload on the wire) you are
otherwise seeing a raw HTTP POST method here. Note these features of the request-responses shown in example above:

- The request line starts with the string POST.
- Content is provided (and thus, a Content-Length header).
- The form parameters are sent as the body.
- The Content-Type for standard web forms is x-www-form-urlencoded.

The most important thing to grasp is that GET and POST are most emphatically not simply two
different ways to format form parameters. Instead, they actually mean two entirely different things. The
GET method means, “I believe that there is a document
at this URL; please return it.” The POST method
means, “Here is an action that I want performed.”


###### Successful Form POSTs Should Always Redirect

In the POST example above you can notice that instead of simply returning a status of 200 followed by a page of weather forecast data, it
instead returned a 302 redirect that urllib2 obeyed by performing a GET for the page named in the
Location: header.

A web site leaves users in a very difficult position if it answers a POST form
submission with a literal web page.Well-designed user-facing POST forms always redirect to a page that shows
the result of the action, and this page can be safely bookmarked, shared, stored, and reloaded. This is an
important feature of modern browsers: if a POST results in a redirect, then pressing the reload button
simply refetches the final URL and does not reattempt the whole train of redirects that lead to the
current location
