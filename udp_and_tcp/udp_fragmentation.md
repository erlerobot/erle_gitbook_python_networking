## UDP Fragmentation

The foregoing program listings have
suggested that a UDP packet can be up to 64kB in size, whereas you probably already know that your
Ethernet or wireless card can only handle packets of around 1,500 bytes instead.

The actual truth is that IP sends small UDP packets as single packets on the wire, but splits up larger UDP
packets into several small physical packets. This means that large
packets are more likely to be dropped, since if any one of their pieces fails to make its way to the destination,
then the whole packet can never be reassembled and delivered to the listening operating system.
But aside from the higher chance of failure, this process of fragmenting large UDP packets so that
they will fit on the wire should be invisible to your application. There are three ways, however, in which
it might be relevant:

- If you are thinking about efficiency, you might want to limit your protocol to small
packets, to make retransmission less likely and to limit how long it takes the
remote IP stack to reassemble your UDP packet and give it to the waiting
application.


- If the ICMP packets are wrongfully blocked by a firewall that would normally allow
your host to auto-detect the MTU between you and the remote host, then your
larger UDP packets might disappear into oblivion without your ever knowing. The
MTU is the “maximum transmission unit” or “largest packet size” that all of the
network devices between two hosts will support.


- If your protocol can make its own choices about how it splits up data between
different packets, and you want to be able to auto-adjust this size based on the
actual MTU between two hosts, then some operating systems let you turn off
fragmentation and receive an error if a UDP packet is too big. This lets you
regroup and split it into several packets if that is possible.

Linux is one operating system that supports this last option. Take a look at `big_sender.py` , which sends
a very large message to one of the servers that we have just designed.

```python
import IN, socket, sys
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
MAX = 65535
PORT = 1060
if len(sys.argv) != 2:
  print >>sys.stderr, 'usage: big_sender.py host'
  sys.exit(2)
hostname = sys.argv[1]
s.connect((hostname, PORT))
s.setsockopt(socket.IPPROTO_IP, IN.IP_MTU_DISCOVER, IN.IP_PMTUDISC_DO)
try:
  s.send('#' * 65000)
except socket.error:
  print 'The message did not make it'
  option = getattr(IN, 'IP_MTU', 14) # constant taken from <linux/in.h>
  print 'MTU:', s.getsockopt(socket.IPPROTO_IP, option)
else:
  print 'The big message was sent! Your network supports really big packets!'
  ```
  If we run this program against a server elsewhere on my home network, then we discover that my
wireless network allows physical packets that are no bigger than the 1,500 bytes typically supported by
Ethernet-style networks:
```
root@erlerobot:~/Python_files#  python big_sender.py 127.0.0.0
The message did not make it
MTU: 1500
```
