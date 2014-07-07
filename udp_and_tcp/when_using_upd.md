## When to use UPD



So, The User Data Protocol, UDP, lets user-level programs send individual packets across an IP network.
Typically, a client program sends a packet to a server, which then replies back using the return address
built into every UDP packet.
You might think that UDP would be very efficient for sending small messages. Actually, UDP is efficient
only if your host ever only sends one message at a time, then waits for a response.


There are two good reasons to use UDP:
- Because you are implementing a protocol that already exists, and it uses UDP.
- Because unreliable subnet broadcast is a great pattern for your application, and
UDP supports it perfectly.
