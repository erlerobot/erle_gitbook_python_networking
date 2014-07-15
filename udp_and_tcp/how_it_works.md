## How UDP works?

The UDP scheme is really quite simple; an IP address and port are all that is necessary to direct a packet to its destination.

Imagine, for example, that you set up a DNS server (Chapter 4) on one of your machines, with the IP address 192.168.1.9. To allow other computers to find the service, the server will ask the operating system for permission to take control of the UDP port with the standard DNS port number 53. Assuming that no process is already running that has claimed that port number, the DNS server will be granted that port.

Next, imagine that a client machine with the IP address 192.168.1.30 on your network is given the IP address of this new DNS server and wants to issue a query. It will craft a DNS query in memory, and then ask the operating system to send that block of data as a UDP packet. Since there will need to be some way to identify the client when the packet returns, and since the client has not explicitly requested a port number, the operating system assigns it a random one—say, port 44137.

The packet will therefore wing its way toward port 53 with labels that identify its source as the IP address and UDP port numbers (here separated by a colon):
```
192.168.1.30:44137
```
And it will give its destination as the following:
```
192.168.1.9:53
```
This destination address, simple though it looks—just the number of a computer, and the number of a port—is everything that an IP network stack needs to guide this packet to its destination. The DNS server will receive the request from its operating system, along with the originating IP and port number. Once it has formulated a response, the DNS server will ask the operating system to send the response as a UDP packet to the IP address and UDP port number from which the request originally came. The reply packet will have the source and destination swapped from what they were in the original packet, and upon its arrival at the source machine, it will be delivered to the waiting client program.
