## Cross-Site Scripting Attacks

The earliest experiments with scripts that could run in web browsers revealed a problem: all of the HTTP
requests made by the browser were done with the authority of the user’s cookies, so pages could cause
quite a bit of trouble by attempting to, say, POST to the online web site of a popular bank asking that
money be transferred to the attacker’s account. Anyone who visited the problem site while logged on to
that particular bank in another window could lose money.
To address this, browsers imposed the restriction that scripts in languages like JavaScript can only
make connections back to the site that served the web page, and not to other web sites. This is called the
“same origin policy.”

Today, would-be attackers find ways
around this policy by using a constellation of attacks called cross-site scripting (known by the acronym
XSS to prevent confusion with Cascading Style Sheets). These techniques include things like finding the
fields on a web page where the site will include snippets of user-provided data without properly
escaping them, and then figuring out how to craft a snippet of data that will perform some
compromising action on behalf of the user or send private information to a third party. Next, the wouldbe
attackers release a link or code containing that snippet onto a popular web site, bulletin board, or in
spam e-mails, hoping that thousands of people will click and inadvertently assist in their attack against
the site.
There are a collection of techniques that are important for avoiding cross-site scripting; you can find
them in any good reference on web development. The most important ones include the following:
- When processing a form that is supposed to submit a POST request, always
carefully disregard any GET parameters.
- Never support URLs that produce some side effect or perform some action simply
through being the subject of a GET.
- In every form, include not only the obvious information—such as a dollar amount
and destination account number for bank transfers—but also a hidden field with a
secret value that must match for the submission to be valid. That way, random
POST requests that attackers generate with the dollar amount and destination
account number will not work because they will lack the secret that would make
the submission valid.

While the possibilities for XSS are not, strictly speaking, problems or issues with the HTTP protocol
itself, it helps to have a solid understanding of them when you are trying to write any program that
operates safely on the World Wide Web.


A library called WebOb is also available for Python (and listed on the Python Package Index) that
contains HTTP request and response classes that were designed from the other direction: that is, they
were intended all along as general-purpose representations of HTTP in all of its low-level details. You
can learn more about them at the WebOb project web page: http://pythonpaste.org/webob/
