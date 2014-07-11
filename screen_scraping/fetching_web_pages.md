## Fetching Web Pages

Before you can parse an HTML-formatted web page, you of course have to acquire some.Here are some options
for downloading content.

- You can use `urllib2`, or the even lower-level `httplib`, to construct an HTTP
request that will return a web page. For each form that has to be filled out, you will
have to build a dictionary representing the field names and data values inside;
unlike a real web browser, these libraries will give you no help in submitting
forms.


- You can to install mechanize and write a program that fills out and submits web
forms much as you would do when sitting in front of a web browser. The downside
is that, to benefit from this automation, you will need to download the page
containing the form HTML before you can then submit it—possibly doubling the
number of web requests you perform.


- If you need to download and parse entire web sites, take a look at the Scrapy
project, hosted at http://scrapy.org, which provides a framework for
implementing your own web spiders. With the tools it provides, you can write
programs that follow links to every page on a web site, tabulating the data you
want extracted from each page.


- When web pages wind up being incomplete because they use dynamic JavaScript
to load data that you need, you can use the QtWebKit module of the PyQt4 library to
load a page, let the JavaScript run, and then save or parse the resulting complete
HTML page.


- Finally, if you really need a browser to load the site, both the Selenium and
Windmill test platforms provide a way to drive a standard web browser from
inside a Python program. You can start the browser up, direct it to the page of
interest, fill out and submit forms, do whatever else is necessary to bring up the
data you need, and then pull the resulting information directly from the DOM
elements that hold them.


