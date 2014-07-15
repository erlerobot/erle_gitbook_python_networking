## Using DNS

PyDNS provides a module for performing DNS queries from python applications.You can install it by:
```
 pip install pydns
```
Your Python interpreter will then gain the ability to run our first DNS program listing, shown in `dns_basic.py`.

```python

import sys, DNS

if len(sys.argv) != 2:
    print >>sys.stderr, 'usage: dns_basic.py <hostname>'
    sys.exit(2)

DNS.DiscoverNameServers()
request = DNS.Request()
for qt in DNS.Type.A, DNS.Type.AAAA, DNS.Type.CNAME, DNS.Type.MX, DNS.Type.NS:
    reply = request.req(name=sys.argv[1], qtype=qt)
    for answer in reply.answers:
        print answer['name'], answer['classstr'], answer['typename'], \
            repr(answer['data'])
            ```
Running this programm will resulto on:
```
root@erlerobot:~/Python_files# dns_basic.py python.org
python.org IN A '82.94.164.162'
python.org IN AAAA ' \x01\x08\x88 \x00\x00\r\x00\x00\x00\x00\x00\x00\x00\xa2'
python.org IN MX (50, 'mail.python.org')
python.org IN NS 'ns2.xs4all.nl'
python.org IN NS 'ns.xs4all.nl'
```
The keys that get printed on each line are as follows:
- The name that we looked up.


- The “class,” which in all queries you are likely to see is IN, meaning it is a question
about Internet addresses.


- The “type” of record; some common ones are A for an IPv4 address, AAAA for an
IPv6 address, NS for a record that lists a nameserver, and MX for a statement about
what mail server should be used for a domain.


- Finally, the “data” provides the information for which the record type was
essentially a promise: the address, or data, or hostname associated with the name
that we asked about.
