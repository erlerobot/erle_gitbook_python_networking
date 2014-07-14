## Connecting and Authenticating

POP supports several authentication methods. The two most common are basic username-password
authentication, and APOP, which is an optional extension to POP that helps protect passwods from
being sent in plain-text if you are using an ancient POP server that does not support SSL.

The process of connecting and authenticating to a remote server looks like this in Python:

1. Create a [POP3_SSL](https://docs.python.org/2/library/poplib.html?highlight=pop3_ssl#poplib.POP3_SSL) or just a plain POP3 object, and pass the remote hostname and
port to it.
2. Call `user()` and `pass_()` to send the username and password. Note the
underscore in `pass_()`. It is present because pass is a keyword in Python and
cannot be used for a method name.
3. If the exception `poplib.error_proto` is raised, it means that the login has failed
and the string value of the exception contains the error explanation sent by the
server.

The choice between POP3 and POP3_SSL is governed by whether your e-mail provider offers—or, in
this day and age, even requires—that you connect over an encrypted connection.

`popconn.py` uses the foregoing steps to log in to a remote POP server. Once connected, it calls
`stat()`, which returns a simple tuple giving the number of messages in the mailbox and the messages’
total size. Finally, the program calls `quit()`, which closes the POP connection.

```python

import getpass, poplib, sys

if len(sys.argv) != 3:
    print 'usage: %s hostname user' % sys.argv[0]
    exit(2)

hostname, user = sys.argv[1:]
passwd = getpass.getpass()

p = poplib.POP3_SSL(hostname)  # or "POP3" if SSL is not supported
try:
    p.user(user)
    p.pass_(passwd)
except poplib.error_proto, e:
    print "Login failed:", e
else:
    status = p.stat()
    print "You have %d messages totaling %d bytes" % status
finally:
    p.quit()
    ```

You can test this program if you have a POP account somewhere.
The program will then prompt you for your password. Finally, it will display the mailbox status,
without touching or altering any of your mail.


When POP servers do not support SSL to protect your connection from snooping, they sometimes at
least support an alternate authentication protocol called APOP, which uses a challenge-response
scheme to assure that your password is not sent in the clear. (But all of your e-mail will still be visible to
any third party watching the packets go by) The Python Standard Library makes this very easy to
attempt: just call the `apop()` method, then fall back to basic authentication if the POP server you are
talking to does not understand.
To use APOP but fall back to plain authentication, you could use a stanza like the one shown below
 inside your POP program (like `ponconn.py`).

 ```python
 print "Attempting APOP authentication..."
try:
  p.apop(user, passwd)
except poplib.error_proto:
  print "Attempting standard authentication..."
  try:
    p.user(user)
    p.pass_(passwd)
  except poplib.error_proto, e:
    print "Login failed:", e
    sys.exit(1)
    ```

