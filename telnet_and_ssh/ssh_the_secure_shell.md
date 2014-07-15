## SSH: The Secure Shell

The SSH protocol is one of the best-known examples of a secure, encrypted protocol among modern
system administrators (HTTPS is probably the very best known).

SSH is descended from an earlier protocol that supported “remote login,” “remote shell,” and
“remote file copy” commands named rlogin, rsh, and rcp, which in their time tended to become much
more popular than Telnet at sites that supported them. You cannot imagine what a revelation rcp was particular, unless you have spent hours trying to transfer a file between computers armed with only
Telnet and a script that tries to type your password for you, only to discover that your file contains a byte
that looks like a control character to Telnet or the remote terminal, and have the whole thing hang until
you add a layer of escaping (or figure out how to disable both the Telnet escape key and all
interpretation taking place on the remote terminal).

But the best feature of the rlogin family was that they did not just echo username and password
prompts without actually knowing the meaning of what was going on. Instead, they stayed involved
through the process of authentication, and you could even create a file in your home directory that told
them “when someone named brandon tries to connect from the asaph machine, just let them in without a
password.” Suddenly, system administrators and Unix users alike received back hours of each month
that would otherwise have been spent typing their password. Suddenly, you could copy ten files from
one machine to another nearly as easily as you could have copied them into a local folder.
SSH has preserved all of these great features of the early remote-shell protocol, while bringing
bulletproof security and hard encryption that is trusted worldwide for administering critical servers.

######An Overview of SSH

At SSH, we reach a protocol so sophisticated that it actually implements its
own rules for multiplexing, so that several “channels” of information can all share the same SSH socket.
Every block of information SSH sends across its socket is labeled with a “channel” identifier so that
several conversations can share the socket.
There are at least two reasons sub-channels make sense. First, even though the channel ID takes up
a bit of bandwidth for every single block of information transmitted, the additional data is small
compared to how much extra information SSH has to transmit to negotiate and maintain encryption
anyway. Second, channels make sense because the real expense of an SSH connection is setting it up.
Host key negotiation and authentication can together take up several seconds of real time, and once the
connection is established, you want to be able to use it for as many operations as possible. Thanks to the
SSH notion of a channel, you can amortize the high cost of connecting by performing many operations
before you let the connection close.
Once connected, you can create several kinds of channels:

- An interactive shell session, like that supported by Telnet.


- The individual execution of a single command.


- A file-transfer session letting you browse the remote filesystem.



- A port-forward that intercepts TCP connections.
