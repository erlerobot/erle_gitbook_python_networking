## Selectors

A selector is a pattern that is crafted to match document elements on which your program wants to
operate.Some of them are:

- People who are deeply XML-centric prefer XPath expressions, which are a
companion technology to XML itself and let you match elements based on their
ancestors, their own identity, and textual matches against their attributes and text
content.



- If you are a web developer, then you probably link to CSS selectors as the most
natural choice for examining HTML.


- Both lxml and BeautifulSoup, as we have seen, provide a smattering of their own
methods for finding document elements.

Here are standards and descriptions for each of the selector styles just described:

- http://www.w3.org/TR/xpath/
- http://codespeak.net/lxml/tutorial.html#using-xpath-to-find-text
- http://codespeak.net/lxml/xpathxslt.html
- http://www.w3.org/TR/CSS2/selector.html
- http://codespeak.net/lxml/cssselect.html

And, finally, here are links to documentation that looks at selector methods peculiar to lxml and
BeautifulSoup:

- http://codespeak.net/lxml/tutorial.html#elementpath
- http://www.crummy.com/software/BeautifulSoup/documentation.html



Now, here you have a completed weather scraper in the file `weather.py`:
```python

import sys, urllib, urllib2
import lxml.etree
from lxml.cssselect import CSSSelector
from BeautifulSoup import BeautifulSoup

if len(sys.argv) < 2:
    print >>sys.stderr, 'usage: weather.py CITY, STATE'
    exit(2)

data = urllib.urlencode({'inputstring': ' '.join(sys.argv[1:])})
info = urllib2.urlopen('http://forecast.weather.gov/zipcity.php', data)
content = info.read()

# Solution #1 using CSSSelector
parser = lxml.etree.HTMLParser(encoding='utf-8')
tree = lxml.etree.fromstring(content, parser)
big = CSSSelector('td.big')(tree)[0]
if big.find('font') is not None:
    big = big.find('font')
print 'Condition:', big.text.strip()
print 'Temperature:', big.findall('br')[1].tail
tr = tree.xpath('.//td[b="Humidity"]')[0].getparent()
print 'Humidity:', tr.findall('td')[1].text
print

# Solution #2 using BeautifulSoup
soup = BeautifulSoup(content)  # doctest: +SKIP
big = soup.find('td', 'big')
if big.font is not None:
    big = big.font
print 'Condition:', big.contents[0].string.strip()
temp = big.contents[3].string or big.contents[4].string  # can be either
print 'Temperature:', temp.replace('&deg;', ' ')
tr = soup.find('b', text='Humidity').parent.parent.parent
print 'Humidity:', tr('td')[1].string
print
```
Take into account that for running this you also need to have the [lxm module](http://lxml.de/installation.html) installed.




