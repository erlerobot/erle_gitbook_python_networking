## Three Axes

Parsing HTML with Python requires three choices:
- The parser you will use to digest the HTML, and try to make sense of its tangle of
opening and closing tags.


- The API(Application Programming Interface) by which your Python program will access the tree of concentric elements
that the parser built from its analysis of the HTML page.


- What kinds of selectors you will be able to write to jump directly to the part of the
page that interests you, instead of having to step into the hierarchy one element at a time.

The issue of selectors is a very important one, because a well-written selector can unambiguously
identify an HTML element that interests you without your having to touch any of the elements above it
in the document tree.

Now, I should pause for a second to explain terms like “deeper,” and I think the concept will be clearest if
we reconsider the unordered list that was quoted in the previous section. An experienced web developer
looking at that list rearranges it in her head, so that this is what it looks like:

<li>First</li>
<li>Second</li>
<li>Third</li>
<li>Fourth</li>
</ul>

```
<ul>
<li>First</li>
<li>Second</li>
<li>Third</li>
<li>Fourth</li>
</ul>```

Here the `<ul>` element is said to be a “parent” element of the individual list items, which “wraps”
them and which is one level “above” them in the whole document. The` <li>` elements are “siblings” of
one another; each is a “child” of the `<ul>` element that “contains” them, and they sit “below” their parent
in the larger document tree. This kind of spatial thinking winds up being very important for working
your way into a document through an API.

In brief, here are your choices along each of the three axes that were just listed:

- The most powerful, flexible, and fastest parser at the moment appears to be the
HTMLParser that comes with lxml; the next most powerful is the longtime favorite
BeautifulSoup ; and coming in dead last are the
parsing classes included with the Python Standard Library, which no one seems to
use for serious screen scraping.


- The best API for manipulating a tree of HTML elements is ElementTree, which has
been brought into the Standard Library for use with the Standard Library parsers,
and is also the API supported by lxml; BeautifulSoup supports an API peculiar to
itself; and a pair of ancient, ugly, event-based interfaces to HTML still exist in the
Python Standard Library.


- The lxml library supports two of the major industry-standard selectors: CSS
selectors and XPath query language; BeautifulSoup has a selector system all its
own, but one that is very powerful and has powered countless web-scraping
programs over the years.
