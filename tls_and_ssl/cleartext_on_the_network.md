## Cleartext on the Network

There are several security problems that TLS is designed to solve. They are best understood by
considering the dangers of sending your network data as “cleartext” over a plain old socket, which copies
your data byte-for-byte into the packets that get sent over the network.

 What are the consequences of someone who can now observe, capture, and analyze your data at his leisure?

 - He can see all of the data that passes over that segment of the network.
The fraction of your data that he can capture depends on how much of it passes
over that particular link.


 - He will see any usernames and passwords that your clients use to connect to the
servers behind them.


 - Log messages can also be intercepted, if they are being sent to a central location
and happen to travel over a compromised IP segment or device. This could be very
useful if the observer wants to probe for vulnerabilities in your software.


 - If your database server is not picky about who connects, aside from caring that the
web front end sends a password, then the attacker can now launch a “replay
attack,” in which he makes his own connection to your database and downloads
all of the data that a front-end server is normally allowed to access.


Imagine an attacker who cannot yet alter traffic on your
network itself, but who can compromise one of the services around the edges that help your servers find
each other. Specifically, what if she can compromise the DNS service that lets your web front ends find
your db.example.com server.Then some interesting tricks might become possible:

- When your front ends ask for the hostname db.example.com, she could answer
with the IP address of her own server, located anywhere in the world, instead.


- The fake database server will be at a loss to answer requests with any
real data that the intruder has not already copied down off the network.


- If your database is not carefully locked down and so is not picky about which
servers connect, then the attacker can do something more interesting: as requests
start arriving at her fake database server, he can have it turn around and forward
those requests to the real database server. This is called a “man-in-the-middle”
attack: he will be
in fairly complete control of your application.


- While proxying the client requests through to the database, the attacker will
probably also have the option of inserting queries of her own into the request
stream. This could let her download entire tables of data and delete or change
whatever data the front-end services are typically allowed to modify.
