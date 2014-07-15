## Shell Sessions and Individual Commands

Once you have a connected SSH client, the entire world of SSH operations is open to you. Simply by
asking, you can access remote-shell sessions, run individual commands, commence file-transfer
sessions, and set up port forwarding.

First, SSH can set up a raw shell session for you, running on the remote end inside a pseudoterminal
so that programs act like they normally do when they are interacting with the user at a terminal.
This kind of connection behaves very much like a Telnet connection; take a look at `ssh_simple.py` for an
example, which pushes a simple echo command at the remote shell, and then asks it to exit.
```python

import paramiko

class AllowAnythingPolicy(paramiko.MissingHostKeyPolicy):
    def missing_host_key(self, client, hostname, key):
        return

client = paramiko.SSHClient()
client.set_missing_host_key_policy(AllowAnythingPolicy())
client.connect('127.0.0.1', username='test')  # password='')

channel = client.invoke_shell()
stdin = channel.makefile('wb')
stdout = channel.makefile('rb')

stdin.write('echo Hello, world\rexit\r')
print stdout.read()

client.close()
```
If you actually run this command, you will see that the commands you type are actually echoed
to you twice, and that there is no obvious way to separate these command echoes from the actual
command output.

Because of quirky terminal-dependent behaviors, you should generally avoid ever using
`invoke_shell()` unless you are actually writing an interactive terminal program where you let a live user
type commands.
A much better option for running remote commands is to use `exec_command()`, which, instead of
starting up a whole shell session, just runs a single command, giving you control of its standard input,
output, and error streams just as though you had run it using the [subprocess module](https://docs.python.org/2/library/subprocess.html?highlight=subprocess#subprocess) in the Standard
Library.As we have seen this module allows you to spawn new processes, connect to their input/output/error pipes, and obtain their return codes.


A script demonstrating its use is shown in `ssh_commands.py`. The difference between `exec_command()`
and a local subprocess
is that you do not get the chance to pass command-line arguments as separate strings; instead, you have
to pass a whole command line for interpretation by the shell on the remote end.
```python


import paramiko

class AllowAnythingPolicy(paramiko.MissingHostKeyPolicy):
    def missing_host_key(self, client, hostname, key):
        return

client = paramiko.SSHClient()
client.set_missing_host_key_policy(AllowAnythingPolicy())
client.connect('127.0.0.1', username='test')  # password='')

for command in 'echo "Hello, world!"', 'uname', 'uptime':
    stdin, stdout, stderr = client.exec_command(command)
    stdin.close()
    print repr(stdout.read())
    stdout.close()
    stderr.close()

client.close()
```

Every time you start a new SSH shell session with `invoke_shell()`, and every time you kick off a
command with `exec_command()`, a new SSH “channel” is created behind the scenes, which is what
provides the file-like Python objects that let you talk to the remote command's standard input, output,
and error. Channels, as just explained, can run in parallel, and SSH will cleverly interleave their data on
your single SSH connection so that all of the conversations happen simultaneously without ever
becoming confused.

Take a look at `ssh_threads.py` for a very simple example of what is possible. Here, two “commands” are
kicked off remotely, which are each a simple shell script with some echo commands interspersed with
pauses created by calls to sleep.The[ threading module](https://docs.python.org/2/library/threading.html?highlight=threading#threading) constructs higher-level threading interfaces on top of the lower level thread module.

```python

import threading
import paramiko

class AllowAnythingPolicy(paramiko.MissingHostKeyPolicy):
    def missing_host_key(self, client, hostname, key):
        return

client = paramiko.SSHClient()
client.set_missing_host_key_policy(AllowAnythingPolicy())
client.connect('127.0.0.1', username='test')  # password='')

def read_until_EOF(fileobj):
    s = fileobj.readline()
    while s:
        print s.strip()
        s = fileobj.readline()

out1 = client.exec_command('echo One;sleep 2;echo Two;sleep 1;echo Three')[1]
out2 = client.exec_command('echo A;sleep 1;echo B;sleep 2;echo C')[1]
thread1 = threading.Thread(target=read_until_EOF, args=(out1,))
thread2 = threading.Thread(target=read_until_EOF, args=(out2,))
thread1.start()
thread2.start()
thread1.join()
thread2.join()

client.close()
```
In order to be able to process these two streams of data simultaneously, we are kicking off two
threads, and are handing each of them one of the channels from which to read. They each print out each
line of new information as soon as it arrives, and finally exit when the `readline()` command indicates
end-of-file by returning an empty string. When run, this script should return something like this:
```
root@erlerobot:~/Python_files# python ssh_threads.py
One
A
B
Two
Three
C
```
SSH channels over the same TCP connection are completely independent, can
each receive (and send) data at their own pace, and can close independently when the particular
command that they are talking to finally terminates.
