## Supporting TLS in Python


From the point of view of your network program, you start a TLS connection by turning control of a
socket over to an SSL library. By doing so, you indicate that you want to stop using the socket for
cleartext communication, and start using it for encrypted data under the control of the library.

From that point on, you no longer use the raw socket; doing so will cause an error and break the
connection. Instead, you will use routines provided by the library to perform all communication. Both
client and server should turn their sockets over to SSL at the same time, after reading all pending data off
of the socket in both directions.
There are two general approaches to using SSL:
- The most straightforward option is probably to use the [ssl package](https://docs.python.org/2/library/ssl.html?highlight=ssl%20package) that recent versions of Python
ship with the Standard Library.
- The other alternative is to use a third-party Python library. There are several of these that support
TLS, but many of them are decrepit and seem to have been abandoned. For example M2Crypto package.
