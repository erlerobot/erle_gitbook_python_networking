## Downloading Pages Through Form Submission

The task of grabbing information from a web site usually starts by reading it carefully with a web browser
and finding a route to the information you need.

Figure `fetch_urllib2.py` shows the site of the National Weather
Service; for our first example, we will write a program that takes a city and state as arguments and prints
out the current conditions, temperature, and humidity.

When using the [urllib2 module](https://docs.python.org/2/library/urllib2.html?highlight=urllib2#urllib2) from the Standard Library, you will have to read the web page
HTML manually to find the form. You can use the View Source command in your browser, search for the
words “Local forecast,” and find the following form in the middle of the sea of HTML:
```
<form method="post" action="http://forecast.weather.gov/zipcity.php" ...>
...
<input type="text" id="zipcity" name="inputstring" size="9"
» value="City, St" onfocus="this.value='';" />
<input type="submit" name="Go2" value="Go" />
</form>
```

The only important elements here are the `<form>` itself and the` <input>` fields inside; everything else
is just decoration intended to help human readers.
This form does a POST to a particular URL with, it appears, just one parameter: an inputstring giving
the city name and state.` fetch_urllib2.py`  shows a simple Python program that uses only the Standard Library
to perform this interaction, and saves the result to phoenix.html.
```python
import urllib, urllib2
data = urllib.urlencode({'inputstring': 'Phoenix, AZ'})
info = urllib2.urlopen('http://forecast.weather.gov/zipcity.php', data)
content = info.read()
open('phoenix.html', 'w').write(content)
```

On the one hand, `urllib2` makes this interaction very convenient; we are able to download a
forecast page using only a few lines of code. But, on the other hand, we had to read and understand the
form ourselves instead of relying on an actual HTML parser to read it. The approach encouraged by
mechanize is quite different: you need only the address of the opening page to get started, and the library
itself will take responsibility for exploring the HTML and letting you know what forms are present. Here
are the forms that it finds on this particular page:
```python
>>> import mechanize
>>> br = mechanize.Browser()
>>> response = br.open('http://www.weather.gov/')
>>> for form in br.forms():
... print '%r %r %s' % (form.name, form.attrs.get('id'), form.action)
... for control in form.controls:
... print ' ', control.type, control.name, repr(control.value)
None None http://search.usa.gov/search
» hidden v:project 'firstgov'
» text query ''
» radio affiliate ['nws.noaa.gov']
» submit None 'Go'
None None http://forecast.weather.gov/zipcity.php
» text inputstring 'City, St'
» submit Go2 'Go'
'jump' 'jump' http://www.weather.gov/
» select menu ['http://www.weather.gov/alerts-beta/']
» button None None
```

Once we have determined that we need the `zipcity.php` form, we can write a program like that
shown in `etch_mechanize.py`. You can see that at no point does it build a set of form fields manually itself, as
was necessary in our previous listing. Instead, it simply loads the front page, sets the one field value that
we care about, and then presses the form’s submit button. Note that since this HTML form did not
specify a name, we had to create our own filter function—the lambda function in the listing—to choose
which of the three forms we wanted.
```python
import mechanize
br = mechanize.Browser()
br.open('http://www.weather.gov/')
br.select_form(predicate=lambda(form): 'zipcity' in form.action)
br['inputstring'] = 'Phoenix, AZ'
response = br.submit()
content = response.read()
open('phoenix.html', 'w').write(content)
```
Many mechanize users instead choose to select forms by the order in which they appear in the
page—in which case we could have called select_form(nr=1). But I prefer not to rely on the order, since
the real identity of a form is inherent in the action that it performs, not its location on a page.
