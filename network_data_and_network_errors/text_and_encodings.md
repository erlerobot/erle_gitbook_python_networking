## Text and Encodings

The use of ASCII for the basic English letters and numbers is nearly universal among network
protocols these days. But when you begin to use more interesting characters, you have to be careful. In
Python you should always represent a meaningful string of text with a “Unicode string” that is denoted
with a leading u, like this:
```python
>>> elvish = u'Namárië!'
```


But you cannot put such strings directly on a network connection without specifying which rival
system of encoding you want to use to mix your characters down to bytes. A very popular system is UTF-
8, because normal characters are represented by the same codes as in ASCII, and longer sequences of
bytes are necessary only for international characters.Other encodings are available in Python; [the Standard Library documentation for the codecs
package](https://docs.python.org/2/library/codecs.html) lists them all. They each represent a full system for reducing symbols to bytes. Here are a few
examples:
```python

>>> elvish.encode('idna')
'xn--namri!-rta6f'
>>> elvish.encode('cp500')
'\xd5\x81\x94E\x99\x89SO'
>>> elvish.encode('utf_8_sig')
'\xef\xbb\xbfNam\xc3\xa1ri\xc3\xab!'
```
On the receiving end of such a string, simply take the byte string and call its decode() method with
the name of the codec that was used to encode it:
```python
>>> 'xn--namri!-rta6f'.decode('idna')
u'nam\xe1ri\xeb!'
>>> '\xd5\x81\x94E\x99\x89SO'.decode('cp500')
u'Nam\xe1ri\xeb!'
>>> '\xef\xbb\xbfNam\xc3\xa1ri\xc3\xab!'.decode('utf_8_sig')
u'Nam\xe1ri\xeb!'
```
