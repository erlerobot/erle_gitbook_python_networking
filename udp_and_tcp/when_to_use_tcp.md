## When to use TCP
TCP has very nearly become a universal default when two programs need to communicate,
we should look at a few instances in which its behavior is not optimal for certain kinds of data, in case an
application you are writing ever falls into one of these categories.
First, TCP is unwieldy for protocols where clients want to send single, small requests to a server, and
then are done and will not talk to it further. It takes three packets for two hosts to set up a TCP
connection—the famous sequence of SYN, SYN-ACK, and ACK (which mean “I want to talk, here is the
packet sequence number I will be starting with”; “okay, here’s mine”; “okay!”)—and then another three
or four to shut the connection back down (either a quick FIN, FIN-ACK, ACK, or a slightly longer pair of
separate FIN and ACK packets). That is six packets just to send a single request:Protocol designers
quickly turn to UDP in such cases.

In view of this we are going to detail two situations where the use of TCP is not appropriate:

- Where UDP really shines over TCP, then, is where such a long-term relationship does not pertain between
client and server, and especially where there are so many clients that a typical TCP implementation
would run out of port numbers if it had to keep up with a separate data stream for each active client.


- The second situation where TCP is inappropriate is when an application can do something much
smarter than simply re-transmit data when a packet has been lost. Imagine an audio chat conversation,
for example: if a second’s worth of data is lost because of a dropped packet, then it will do little good to
simply re-send that same second of audio, over and over, until it finally arrives.
