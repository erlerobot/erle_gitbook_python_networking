## Decoding Headers

The last trick that we should cover regarding MIME messages is decoding headers that may have been
encoded with foreign languages. The function `decode_header()` takes a single header and returns a list of
pieces of the header; each piece is a binary string together with its encoding (named as a string if it is
something besides 7-bit ASCII, else the value None):
```python
>>> x = '=?iso-8859-1?q?Michael_M=FCller?= <mmueller@example.com>'
>>> import email.header
>>> pieces = email.header.decode_header(x)
>>> print pieces
[('Michael M\xfcller', 'iso-8859-1'), ('<mmueller@example.com>', None)]
```
Of course, this raw information is likely to be of little use to you. To instead see the actual text inside
the encoding, use the `decode()` function of each binary string in the list (falling back to an ‘ascii’
encoding if None was returned) and paste the result together with spaces:
```python
>>> print ' '.join( s.decode(enc or 'ascii') for s,enc in pieces )
Michael Müller <mmueller@example.com>
```
It is always good practice to use `decode_header()` on any of the “big three” headers —From, To, and
Subject —before displaying them to the user. If no special encoding was used, then the result will simply
be a one-element list containing the header string with a None encoding.
