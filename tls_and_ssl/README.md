# TLS and SSL
---

Before you send sensitive data across a network, you need proof of the identity of
the machine that you think is on the other end of the socket, and while sending the data, you need it
protected against the prying eyes of anyone controlling the gateways and network switches that see all of
your packets. The solution to this problem is to use Transport Layer Security (TLS). Because earlier
versions of TLS were called the Secure Sockets Layer (SSL), nearly all of the libraries that you will use to
speak TLS actually still have SSL somewhere in the name.
