## SSH Authentication

 Since this chapter is primarily
about how to “speak SSH” from Python, I will just briefly outline how authentication works.
There are generally three ways to prove your identity to a remote server you are contacting through
SSH:

- You can provide a username and password.


- You can provide a username, and then have your client successfully perform a
public-key challenge-response. This clever operation manages to prove that you
are in possession of a secret “identity” key without actually exposing its contents
to the remote system.


- You can perform [Kerberos](https://pypi.python.org/pypi/kerberos/1.1.1) authentication. If the remote system is set up to allow
Kerberos, and if you have run the
kinit command-line tool to prove your identity to one of the master Kerberos
servers in the SSH server's authentication domain, then you should be allowed in
without a password.

Since option 3 is very rare, we will concentrate on the first two.
Using a username and password with `paramiko` is very easy—you simply provide them in your call to
the `connect()` method:
```python
>>> client.connect('my.example.com', username='brandon', password=mypass)
```
Public-key authentication, where you use ssh-keygen to create an “identity” key pair (which is
typically stored in your ~/.ssh directory) that can be used to authenticate you without a password,
makes the Python code even easier.
```python
>>> client.connect('my.example.com')
```
If your identity key file is stored somewhere other than in the normal ~/.ssh/id_rsa file, then you
can provide its file name—or a whole Python list of file names—to the `connect()` method manually:
```python
>>> client.connect('my.example.com',key_filename='/home/brandon/.ssh/id_sysadmin')
```
Once the `connect()` method has succeeded, you are now ready to start performing remote
operations, all of which will be forwarded over the same physical socket without requiring re-negotiation
of the host key, your identity, or the encryption that protects the SSH socket itself.
