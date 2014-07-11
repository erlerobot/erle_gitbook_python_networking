## Cookies


The actual mechanism that powers user identity tracking, logging in, and logging out of modern web
sites is the cookie. The HTTP responses sent by a server can optionally include a number of Set-cookie:
headers that browsers store on behalf of the user. In every subsequent request made to that site,the browser will include a Cookie: header
corresponding to each cookie that has been set.


The most obvious use os cookies is to keep up with user identity. To support logging in, a web site can deploy a
normal form that asks for your username and password (or e-mail address and password, or whatever).

Cookies can also be used for feats other than simply identifying users. For example, a site can issue a
cookie to every browser that connects, enabling it to track even casual visitors. This approach enables an
online store to let visitors start building a shopping cart full of items without ever being forced to create an account.


From the point of view of a web client, cookies are moderately short strings that have to be stored
and then divulged when matching requests are made. The Python Standard Library puts this logic in its
own module, `cookielib`(The [cookielib module](https://docs.python.org/2/library/cookielib.html?highlight=cookielib#cookielib) defines classes for automatic handling of HTTP cookies.), whose CookieJar objects can be used as small cookie databases by the
HTTPCookieProcessor in `urllib2. To see its effect, you need go no further than the front page of Google,
which sets cookies in the mere event of an unknown visitor arriving at the site for the first time. Here is
how we create a new opener that knows about cookies:
```python
>>> import cookielib
>>> cj = cookielib.CookieJar()
>>> cookie_opener = urllib2.build_opener(VerboseHTTPHandler,
... urllib2.HTTPCookieProcessor(cj))
```
Opening the Google front page will result in two different cookies getting set:
```
>>> response = cookie_opener.open('http://www.google.com/')
--------------------------------------------------
GET / HTTP/1.1
...
-------------------- Response --------------------
HTTP/1.1 200 OK
...
Set-Cookie: PREF=ID=94381994af6d5c77:FF=0:TM=1288205983:LM=1288205983:S=Mtwivl7EB73uL5Ky;􀀁
expires=Fri, 26-Oct-2012 18:59:43 GMT; path=/; domain=.google.com
Set-Cookie: NID=40=rWLn_I8_PAhUF62J0yFLtb1-AoftgU0RvGSsa81FhTvd4vXD91iU5DOEdxSVt4otiISY-􀀁
3RfEYcGFHZA52w3-85p-hujagtB9akaLnS0QHEt2v8lkkelEGbpo7oWr9u5; expires=Thu, 28-Apr-2011􀀁
18:59:43 GMT; path=/; domain=.google.com; HttpOnly
...

```
If you investigate more about `cookielib`,you will find that you can do more than query and
modify the cookies that have been set. You can also automatically store them in a file, so that they
survive from one Python session to the next. You can even create cookie processors that implement your
own custom policies with respect to which cookies to store and which to divulge.


Servers can constrain a cookie to a particular domain and path, in addition to setting a Max-age or
expires time. Unfortunately, some browsers ignore this setting, so sites should never base their security
on the assumption that the expires time will be obeyed. Therefore, servers can mark cookies as secure;
this ensures that such cookies are only transmitted with HTTPS requests to the site and never in
unsecure HTTP requests.









