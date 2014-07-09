## Compression

Since the time necessary to transmit data over the network is often more significant than the time your
CPU spends preparing the data for transmission, it is often worthwhile to compress data before sending
it. The popular HTTP protocol lets a client and server figure out whether they
can both support compression.

An interesting fact about the most ubiquitous form of compression, the GNU [`zlib` facility](https://docs.python.org/2/library/zlib.html?highlight=zlib#zlib) (For applications that require data compression, the functions in this module allow compression and decompression, using the zlib library) that is
available through the Python Standard Library, is that it is self-framing. If you start feeding it a
compressed stream of data, then it can tell you when the compressed data has ended and further,
uncompressed data has arrived past its end.

Most protocols choose to do their own framing and then, if desired, pass the resulting block to zlib
for decompression. But you could conceivably promise yourself that you would always tack a bit of
uncompressed data onto the end of each zlib compressed string—here, we will use a single '.' byte—
and watch for your compression object to split out that “extra data” as the signal that you are done.
Consider this combination of two compressed data streams:
```python
>>> import zlib
>>> data = zlib.compress('sparse') + '.' + zlib.compress('flat') + '.'
>>> data
'x\x9c+.H,*N\x05\x00\t\r\x02\x8f.x\x9cK\xcbI,\x01\x00\x04\x16\x01\xa8.'
>>> len(data)
28
```
Imagine that these 28 bytes arrive at their destination in 8-byte packets. After processing the first
packet, we will find the decompression object's `unused_data` slot still empty, which tells us that there is
still more data coming, so we would `recv()` on our socket again:
```python
>>> dobj = zlib.decompressobj()
>>> dobj.decompress(data[0:8]), dobj.unused_data
('spars', '')
```
But the second block of eight characters, when fed to our decompress object, both finishes out the
compressed data we were waiting for (since the final 'e' completes the string 'sparse') and also finally
has a non-empty unused_data value that shows us that we finally received our '.' byte:
```python
>>> dobj.decompress(data[8:16]), dobj.unused_data
('e', '.x')
```
If another stream of compressed data is coming, then we have to provide everything past the '.'—
in this case, the character 'x'—to our new decompress object, then start feeding it the remaining
“packets”:

```python
>>> dobj2 = zlib.decompressobj()
>>> dobj2.decompress('x'), dobj2.unused_data
('', '')
>>> dobj2.decompress(data[16:24]), dobj2.unused_data
('flat', '')
>>> dobj2.decompress(data[24:]), dobj2.unused_data
('', '.')
```
At this point, unused_data is again non-empty, meaning that we have read past the end of this
second bout of compressed data and can examine its content.
