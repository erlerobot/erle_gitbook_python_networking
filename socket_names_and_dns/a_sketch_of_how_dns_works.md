## A Sketch of How DNS Works

The DNS Protocol purpose is to turn hostnames into IP addresses.

For example, consider the domain name www.python.org. If your web browser needs to know this
address, then the browser runs a call like `getaddrinfo()` to ask the operating system to resolve that name. Your system will know either that it is running a nameserver of its own, or that the network to
which it is attached provides name service.So, the first act of your DNS server will be to check its own cache of recently queried domain names to
see if www.python.org has already been checked by some other machine served by the DNS server in the
last few minutes or hours. If an entry is present and has not yet expired—and the owner of each domain
name gets to choose its expiration timeout, because some organizations like to change IP addresses
quickly if they need to, while others are happy to have old IP addresses linger for hours or days in the
world's DNS caches—then it can be returned immediately. But let us imagine that it is morning and that
you are the first person in your office or in the coffee shop to try talking to www.python.org today, and so
the DNS server has to go find the hostname from scratch.
Your DNS server will now begin a recursive process of asking about www.python.org at the very top of
the world's DNS server hierarchy: the “root-level” nameservers that know all of the top-level domains
(TLDs) like .com, .org, .net, and all of the country domains, and know the groups of servers that are
responsible for each. Nameserver software generally comes with the IP addresses of these top-level
servers built in, to solve the bootstrapping problem of how you find any domain nameservers before you
are actually connected to the domain name system.With this first UDP round-trip, your DNS server will
learn (if it did not know already from another recent query) which servers keep the full index of .org
domain.

Now a second DNS request will be made, this time to one of the .org servers, asking who on earth
runs the python.org domain. You can find out what those top-level servers know about a domain by
running the whois command-line program on a POSIX system, or use one of the many “whois” web
pages online, typing:
```
whois python.org
```
Wherever you are in the world, your DNS request for any hostname
within python.org must be passed on to one of the two DNS servers named in that entry.


There are some reasond to **not use DNS**, and use `getaddrinfo()` or some other system-supported mechanism for resolving hostnames.


The DNS is often not the only way that a system gets name information.
- If your application runs off and tries to use DNS on its own as its first choice for resolving
a domain name, then users will notice that some computer names that work
everywhere else on your system—in their browser, in file share names, and so
forth—suddenly do not work when they use your application, because you are not
deferring to mechanisms like WINS or /etc/hosts like the operating system itself
does.
- The local machine probably has a cache of recently queried domain names that
might already know about the host whose IP address you need. If you try speaking
DNS yourself to answer your query, you will be duplicating work that has already
been done.
- The system on which your Python script is running already knows about the local
domain nameservers, thanks either to manual intervention by your system
administrator or a network configuration protocol like DHCP in your office, home,
or coffee shop. To crank up DNS right inside your Python program, you will have
to learn how to query your particular operating system for this information—an
operating-system-specific action that we will not be covering in this book.
- If you do not use the local DNS server, then you will not be able to benefit from its
own cache that would prevent your application and other applications running on
the same network from repeating requests about a hostname that is in frequent
use at your location.
- From time to time, adjustments are made to the world DNS infrastructure, and
operating system libraries and daemons are gradually updated to accommodate
this. If your program makes raw DNS calls of its own, then you will have to follow
these changes yourself and make sure that your code stays up-to-date with the
latest changes in TLD server IP addresses, conventions involving
internationalization, and tweaks to the DNS protocol itself.

There is, however, a solid and legitimate reason to make a DNS call from Python: because you are a mail
server, or at the very least a client trying to send mail directly to your recipients without needing to run a
local mail relay, and you want to look up the MX records associated with a domain so that you can find
the correct mail server for your friends at @example.com.
