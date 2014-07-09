## Network Byte Order

To understand the issue of byte order, consider the process of sending an integer over the network.
To be specific, think about the integer 4253.

Many protocols, of course, will simply transmit this integer as the string '4253'—that is, as four
distinct characters. The four digits will require at least four bytes to transmit, at least in any common text
encoding. And using decimal digits will also involve some computational expense: since numbers are
not stored inside computers in base 10, it will take repeated division—with inspection of the
remainder—to determine that this number is in fact made of 4 thousands, plus 2 hundreds, plus 5 tens,
plus 3 left over. And when the four-digit string '4253' is received, repeated addition and multiplication
by powers of ten will be necessary to put the text back together into a number.

In any case, the string '4253' is not how your computer represents this number as an integer
variable in Python. Instead it will store it as a binary number, using the bits of several successive bytes to
represent the one's place, two's place, four's place, and so forth of a single large number. We can glimpse
the way that the integer is stored by using the hex() built-in function at the Python prompt:
```python
>>> hex(4253)
'0x109d'
```
Each hex digit corresponds to four bits, so each pair of hex digits represents a byte of data. Instead of
being stored as four decimal digits 4, 4, 2, and 3 with the first 4 being the “most significant” digit (since
tweaking its value would throw the number off by a thousand) and 3 being its least significant digit, the
number is stored as a most significant byte 0x10 and a least significant byte 0x9d, adjacent to one another
in memory.

Here we reach a great difference between
computers.While they will all agree that the bytes in memory have an order, and they will all store a
string like Content-Length: 4253 in exactly that order starting with C and ending with 3, they do not share
a single idea about the order in which the bytes of a binary number should be stored.
Some computers are “big-endian” and put the most
significant byte first; others are “little-endian” and put the least significant byte first.

Python makes it very easy to see the difference between the two endiannesses. Simply use the struct
module, which provides a variety of operations for converting data to and from popular binary formats.
Here is the number 4253 represented first in a little-endian format and then in a big-endian order:
```python
>>> import struct
>>> struct.pack('<i', 4253)
'\x9d\x10\x00\x00'
>>> struct.pack('>i', 4253)
'\x00\x00\x10\x9
```
`struct` module performs conversions between Python values and C structs represented as Python strings.You can read more [here](https://docs.python.org/2/library/struct.html?highlight=struct#struct).We here used the code i, which uses four bytes to store an integer, so the two upper bytes are zero for  asmall number like 4253.It also
supports an `unpack()` operation, which converts the binary data back to Python numbers:
```python
>>> struct.unpack('>i', '\x00\x00\x10\x9d')
(4253,)
```
Therefore the
struct module provides another symbol, '!', which means the same thing as '>' when used in `pack()`
and `unpack()` but says to other programmers (and, of course, to yourself as you read the code later), “I
am packing this data so that I can send it over the network.”


