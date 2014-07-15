## Sending E-Mail

Successfully sending e-mail generally requires a queue where a
message can sit for seconds, minutes, or days until it can be successfully transmitted toward its
destination. So you typically do not want your programs using Python’s [smtplib](https://docs.python.org/2/library/smtplib.html?highlight=smtplib#smtplib) to send mail directly to a
message’s destination—because if your first transmission attempt fails, then you will be stuck with the job of writing a full “mail transfer agent” (MTA), as the RFCs call an e-mail server, and give it a full
standards-compliant re-try queue. This is not only a big job, but also one that has already been done
well several times, and you will be wise to take advantage of one of the existing MTAs (look at postfix,
exim, and qmail) before trying to write something of your own.

So only rarely will you be making SMTP connections out into the world from Python. More usually,
your system administrator will tell you one of two things:
- That you should make an authenticated SMTP connection to an existing e-mail
server, using a username and password that will belong to your application, and
give it permission to use the e-mail server to queue outgoing messages


- That you should run a local binary on the system—like the sendmail program—
that the system administrator has already gone to the trouble to configure so that
local programs can send mail.
