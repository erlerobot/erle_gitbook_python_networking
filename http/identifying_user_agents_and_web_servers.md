## Identifying User Agents and Web Servers

`User-Agent: Python-urllib/2.6`:
This header is optional in the HTTP protocol, and many sites simply ignore or log it. It can be useful
when sites want to know which browsers their visitors use most often, and it can sometimes be used to
distinguish search engine spiders (bots) from normal users browsing a site.

Many web sites are sensitive to the kinds of browsers that view them. If you need to
access such sites with `urllib2, you can simply instruct it to lie about its identity, and the receiving web
site will not know the difference:
```python
>>> url = 'https://wca.eclaim.com/'
>>> urllib2.urlopen(url).read()
'<HTML>...The following are...required...Microsoft Internet Explorer...'
>>> agent = 'Mozilla/5.0 (Windows; U; MSIE 7.0; Windows NT 6.0; en-US)'
>>> request = urllib2.Request(url)
>>> request.add_header('User-Agent', agent)
>>> urllib2.urlopen(request).read()
'\r\n<HTML>\r\n<HEAD>\r\n\t<TITLE>Eclaim.com - Log In</TITLE>...'
```
There are databases of possible user agent strings online at several sites that you can reference both
when analyzing agent strings that your own servers have received, as well as when concocting strings for
your own HTTP requests:
- http://www.zytrax.com/tech/web/browser_ids.htm
- http://www.useragentstring.com/pages/useragentstring.php
