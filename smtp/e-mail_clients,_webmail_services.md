# E-mail Clients, Webmail Services

The role of SMTP in message submission, where the user presses “Send” and expects a message to go
winging its way across the Internet, will probably be least confusing if we trace the history of how users
have historically worked with Internet mail.
The key concept to understand as we begin this history is that users have never been asked to sit
around and wait for an e-mail message to actually be delivered. This process can often take quite a bit of
time—and up to several dozen repeated attempts—before an e-mail message is actually delivered to its
destination. Any number of things could cause delays: a message could have to wait because other
messages are already being transmitted across a link of limited bandwidth; the destination server might
be down for a few hours, or its network might not be currently accessible because of a glitch; and if the
mail is destined for a large organization, then it might have to make several different “hops” as it arrives
at the big university server, then is directed to a smaller college e-mail machine, and then finally is
directed to a departmental e-mail server.

The role of SMTP in message submission, where the user presses “Send” and expects a message to go
winging its way across the Internet, will probably be least confusing if we trace the history of how users
have historically worked with Internet mail.
The key concept to understand as we begin this history is that users have never been asked to sit
around and wait for an e-mail message to actually be delivered. This process can often take quite a bit of
time—and up to several dozen repeated attempts—before an e-mail message is actually delivered to its
destination. Any number of things could cause delays: a message could have to wait because other
messages are already being transmitted across a link of limited bandwidth; the destination server might
be down for a few hours, or its network might not be currently accessible because of a glitch; and if the
mail is destined for a large organization, then it might have to make several different “hops” as it arrives
at the big university server, then is directed to a smaller college e-mail machine, and then finally is
directed to a departmental e-mail server.

E-mail browsing and submission, therefore, become a black box: your browser interacts with a web
API, and on the other end, you will see plain old SMTP connections originating from and going to the
large organization as mail is delivered in each direction. But in the world of webmail, client protocols are
removed from the equation, taking us back to the old days of pure server-to-server unauthenticated
SMTP.
