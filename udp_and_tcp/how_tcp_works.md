## How TCP works

First, every packet is given a sequence number, so that the system on the receiving end can put
them back together in the right order, and so that it can notice missing packets in the sequence and ask
that they be re-transmitted.
Instead of using sequential integers (1,2,…) to mark packets, TCP uses a counter that counts the
number of bytes transmitted. So a 1,024-byte packet with a sequence number of 7,200 would be followed
by a packet with a sequence number of 8,224. This means that a busy network stack does not have to
remember how it broke a data stream up into packets; if asked for a re-transmission, it can break the
stream up into packets some other way (which might let it fit more data into a packet if more bytes are
now waiting for transmission), and the receiver can still put the packets back together.

Rather than running very slowly in lock-step by needing every packet to be acknowledged before it
sends the next one, TCP sends whole bursts of packets at a time before expecting a response. The
amount of data that a sender is willing to have on the wire at any given moment is called the size of the
TCP “window.”
The TCP implementation on the receiving end can regulate the window size of the transmitting end,
and thus slow or pause the connection. This is called “flow control.” This lets it forbid the transmission
of additional packets in cases where its input buffer is full and it would have to discard any more data if it
were to arrive right now.

Finally, if TCP sees that packets are being dropped, it assumes that the network is becoming
congested and stops sending as much data every second.
