## How SMTP Is Used

The foregoing narrative has hopefully helped you structure your thinking about Internet e-mail
protocols, and realize how they fit together in the bigger picture of getting messages to and from users.
But the subject of this chapter is a narrower one—the Simple Mail Transport Protocol in particular.
And we should start by stating the basics:

- SMTP is a TCP/IP-based protocol.
- Connections can be authenticated, or not.
- Connections can be encrypted, or not.

Most e-mail connections across the Internet these days seem to lack any attempt at encryption,
which means that whoever owns the Internet backbone routers are theoretically in a position to read
simply staggering amounts of other people’s mail.

What are the two ways that SMTP is used?
First, SMTP can be used for e-mail submission between a client e-mail program like Thunderbird or
Outlook, claiming that a user wants to send e-mail, and a server at an organization that has given that
user an e-mail address. These connections generally use authentication, so that spammers cannot
connect and send millions of messages on a user’s behalf without his or her password. Once received,
the server puts the message in a queue for delivery (and often makes its first attempt at sending it
moments later), and the client can forget about the message and presume the server will keep trying to
deliver it.
Second, SMTP is used between Internet mail servers as they move e-mail from its origin to its
destination. This typically involves no authentication; after all, big organizations like Google, Yahoo!,
and Microsoft do not know the passwords of each other’s users, so when Yahoo! receives an e-mail from
Google claiming that it was sent from an @gmail.com user, Yahoo! just has to believe them (or not—
sometimes organizations blacklist each other if too much spam is making it through their servers, as
happened to a friend of mine the other day when Hotmail stopped accepting his client’s newsletters
from GoDaddy’s servers because of alleged problems with spam).

So, typically, no authentication takes place between servers talking SMTP to each other—and even
encryption against snooping routers seems to be used only rarely.
Because of the problem of spammers connecting to e-mail servers and claiming to be delivering
mail from another organization’s users, there has been an attempt made to lock down who can send email
on an organization’s behalf. Though controversial, some e-mail servers consult the Sender Policy
Framework (SPF), defined in RFC 4408, to see whether the server they are talking to really has the
authority to deliver the e-mails it is transmitting.
But the SPF and other anti-spam technologies are unfortunately beyond the scope of this book,
which must limit itself to the question of using the basic protocols themselves from Python. So we now
turn to the more technical question of how you will actually use SMTP from your Python programs.
