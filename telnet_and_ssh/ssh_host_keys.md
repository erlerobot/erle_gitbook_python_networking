## SSH Host Keys

When an SSH client first connects to a remote host, they exchange temporary public keys that let them
encrypt the rest of their conversation without revealing any information to any watching third parties.
Then, before the client is willing to divulge any further information, it demands proof of the remote
server's identity. This makes good sense as a first step: if you are really talking to a hacker who has
temporarily managed to grab the remote server's IP, you do not want SSH to divulge even your
username—much less your password.

There are many problems with this system from the point of view of SSH. While it is true that you
can build a public-key infrastructure internal to an organization, where you distribute your own signing
authority's certificates to your web browsers or other applications and then can sign your own server
certificates without paying a third party, a public-key infrastructure is still considered too cumbersome a
process for something like SSH; server administrators want to set up, use, and tear down servers all the
time, without having to talk to a central authority first.

So SSH has the idea that each server, when installed, creates its own random public-private key pair
that is not signed by anybody. Instead, one of two approaches is taken to key distribution:

- A system administrator writes a script that gathers up all of the host public keys in
an organization, creates an `ssh_known_hosts` listing them all, and places this file in
the /etc/sshd directory on every system in the organization. Now
every SSH client will know about every SSH host key before they even connect for
the first time.


- Abandon the idea of knowing host keys ahead of time, and instead memorize
them at the moment of first connection. Users of the SSH command line will be
very familiar with this: the client says it does not recognize the host to which you
are connecting, you reflexively answer “yes,” and its key gets stored in your
"~/.ssh/known_hosts" file. You actually have no guarantee that you are really talking
to the host you think it is; but at least you will be guaranteed that every
subsequent connection you ever make to that machine is going to the right place,
and not to other servers that someone is swapping into place at the same IP
address.

The familiar prompt from the SSH command line when it sees an unfamiliar host looks like this:
```
root@erlerobot:~# ssh asaph.rhodesmill.org
The authenticity of host 'asaph.rhodesmill.org (74.207.234.78)'
can't be established.
RSA key fingerprint is 85:8f:32:4e:ac:1f:e9:bc:35:58:c1:d4:25:e3:c7:8c.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'asaph.rhodesmill.org,74.207.234.78' (RSA)
to the list of known hosts.
```
That “yes” answer buried deep on the next-to-last full line is the answer that I typed giving SSH the
go-ahead to make the connection and remember the key for next time.

The [paramiko](https://pypi.python.org/pypi/paramiko/1.14.0) library has full support for all of the normal SSH tactics surrounding host keys. But its
default behavior is rather spare: it loads no host-key files by default, and will then, of course, raise an
exception for the very first host to which you connect because it will not be able to verify its key. The
exception that it raises is a bit un-informative; it is only by looking at the fact that it comes from inside
the `missing_host_key(`) function that I usually recognize what has caused the error.(Before doing this, install paramiko module from Python Package Index):
```python
>>> import paramiko
>>> client = paramiko.SSHClient()
>>> client.connect('my.example.com', username='test')
Traceback (most recent call last):
...
File ".../paramiko/client.py", line 85, in missing_host_key
» raise SSHException('Unknown server %s' % hostname)
paramiko.SSHException: Unknown server my.example.com
```
To behave like the normal SSH command, load both the system and the current user's known-host
keys before making the connection:
```python
>>> client.load_system_host_keys()
>>> client.load_host_keys('/home/brandon/.ssh/known_hosts')
>>> client.connect('my.example.com', username='test')
```
The `paramiko` library also lets you choose how you handle unknown hosts. Once you have a client
object created, you can provide it with a decision-making class that is asked what to do if a host key is
not recognized. You can build these classes yourself by inheriting from the MissingHostKeyPolicy class:
```python
>>> class AllowAnythingPolicy(paramiko.MissingHostKeyPolicy):
... def missing_host_key(self, client, hostname, key):
... return
...
>>> client.set_missing_host_key_policy(AllowAnythingPolicy())
>>> client.connect('my.example.com', username='test')
```
Note that, through the arguments to the `missing_host_key()` method, you receive several pieces of
information on which to base your decision; you could, for example, allow connections to machines on
your own server subnet without a host key, but disallow all others.

Inside `paramiko` there are also several decision-making classes that already implement several basic
host-key options:

- `paramiko.AutoAddPolicy`: Host keys are automatically added to your user host-key
store (the file ~/.ssh/known_hosts on Unix systems) when first encountered, but
any change in the host key from then on will raise a fatal exception.


- ` paramiko.RejectPolicy`: Connecting to hosts with unknown keys simply raises an
exception.


- `paramiko.WarningPolicy`: An unknown host causes a warning to be logged, but the
connection is then allowed to proceed.


The AutoAddPolicy
never needs human interaction, but will at least assure you on subsequent encounters that you are still
talking to the same machine as before.
