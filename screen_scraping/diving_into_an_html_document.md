## Diving into an HTML Document

The tree of objects that a parser creates from an HTML file is often called a Document Object Model, or
DOM, even though this is officially the name of one particular API defined by the standards bodies and
implemented by browsers for the use of JavaScript running on a web page.

The task we have set for ourselves, you will recall, is to find the current conditions, temperature, and
humidity in the phoenix.html page that we have downloaded

There are two approaches to narrowing your attention to the specific area of the document in which
you are interested. You can either search the HTML for a word or phrase close to the data that you want,
or, as we mentioned previously, use Google Chrome or Firefox with Firebug to “Inspect Element” and
see the element you want embedded in an attractive diagram of the document tree.

To see how direct document-object manipulation would work in this case, we can load the raw page
directly into both the lxml and [BeautifulSoup](https://pypi.python.org/pypi/BeautifulSoup/3.2.1) systems.
```python

>>> import lxml.etree
>>> parser = lxml.etree.HTMLParser(encoding='utf-8')
>>> tree = lxml.etree.parse('phoenix.html', parser)
```
The need for a separate parser object here is because, as you might guess from its name, lxml is
natively targeted at XML files.
```python
>>> from BeautifulSoup import BeautifulSoup
>>> soup = BeautifulSoup(open('phoenix.html'))
Traceback (most recent call last):
...
HTMLParseError: malformed start tag, at line 96, column 720
```
What on earth? Well, look, the National Weather Service does not check or tidy its HTM. Jumping to line 96, column 720 of phoenix.html, we see that
there does indeed appear to be some broken HTML:
```
<a href="http://www.weather.gov"<u>www.weather.gov</u></a>
```
You can see that the `<u> `tag starts before a closing angle bracket has been encountered for the `<a>`
tag. But why should BeautifulSoup care. I wonder what version I have installed.
```python
>>> BeautifulSoup.__version__
'3.1.0'
```
Well, drat. I typed too quickly and was not careful to specify a working version when I ran pip to
install BeautifulSoup into my virtual environment. Let’s try again:

```
root@erlerobot:~/Python_files# pip install BeautifulSoup==3.0.8.1
```

Now, if we were to take the approach of starting at the top of the document and digging ever deeper
until we find the node that we are interested in, we are going to have to generate some very verbose
code. Here is the approach we would have to take with lxml:
```python
>>> fonttag = tree.find('body').find('div').findall('table')[3] \
... .findall('tr')[1].find('td').findall('table')[1].find('tr') \
... .findall('td')[1].findall('table')[1].find('tr').find('td') \
... .find('table').findall('tr')[1].find('td').find('table') \
... .find('tr').find('td').find('font')
>>> fonttag.text
'\nA Few Clouds'
```
An attractive syntactic convention lets BeautifulSoup handle some of these steps more beautifully:
```python
>>> fonttag = soup.body.div('table', recursive=False)[3] \
... ('tr', recursive=False)[1].td('table', recursive=False)[1].tr \
... ('td', recursive=False)[1]('table', recursive=False)[1].tr.td \
... .table('tr', recursive=False)[1].td.table \
... .tr.td.font
>>> fonttag.text
u'A Few Clouds71&deg;F(22&deg;C)'
```

BeautifulSoup lets you choose the first child element with a given tag by simply selecting the
attribute .tagname, and lets you receive a list of child elements with a given tag name by calling an
element like a function with the tag name and a
recursive option telling it to pay attention just to the children of an element.


Both lxml and BeautifulSoup provide attractive ways to quickly grab a child element based on
its tag name and position in the document.
We clearly should not be using such primitive navigation to try descending into a real-world
web page.

Figuring out how HTML elements are grouped, by the way, is much easier if you either view HTML
with an editor that prints it as a tree, or if you run it through a tool like HTML tidy from W3C that can
indent each tag to show you which ones are inside which other ones.
`tidy` validate, correct, and pretty-print HTML files. You should use this command line:
```
 tidy phoenix.html > phoenix-tidied.html
 ```
