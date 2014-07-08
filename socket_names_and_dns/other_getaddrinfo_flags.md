# Other getaddrinfo() Flags

The flags
available vary somewhat by operating system, and you should always consult your own computer's
documentation (not to mention its configuration) if you are confused about a value that it chooses to
return. But there are several flags that tend to be cross-platform; here are some of the more important ones:

- AI_ALL: W AI_V4MAPPED option will save you in the
situation where you are on a purely IPv6-connected host, but the host to which
you want to connect advertises only IPv4 addresses: it resolves this problem by
“mapping” the IPv4 addresses to their IPv6 equivalent. But if some IPv6 addresses
do happen to be available, then they will be the only ones shown. Thus the
existence of this option: if you want to see all of the addresses from your IPv6-
connected host, even though some perfectly good IPv6 addresses are available,
then combine this AI_ALL flag with AI_V4MAPPED and the list returned to you will
have every address known for the target host.
- AI_NUMERICHOST: This turns off any attempt to interpret the hostname parameter
(the first parameter to `getaddrinfo()) as a textual hostname like cern.ch, and only
tries to interpret the hostname string as a literal IPv4 or IPv6 hostname like
74.207.234.78 or fe80::fcfd:4aff:fecf:ea4e. This is much faster, as no DNS
round-trip is incurred (see the next section), and prevents possibly untrusted user
input from forcing your system to issue a query to a nameserver under someone
else's control.
- AI_NUMERICSERV: This turns off symbolic port names like www and insists that port
numbers like 80 be used instead. This does not necessarily have the networkquery
implications of the previous option, since port-number databases are
typically stored locally on IP-connected machines; on POSIX systems, resolving a
symbolic port name typically requires only a quick scan of the /etc/services file
(but check your /etc/nsswitch.conf file's services option to be sure). But if you
know your port string should always be an integer, then activating this flag can be
a useful sanity check.
