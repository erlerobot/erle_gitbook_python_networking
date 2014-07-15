## Command-Line Automation

Before getting into the details of how the command line works, and how you can access it over the
network, we should pause and note that there exist many systems today for automating the entire
process.If you have dozens or hundreds of machines to maintain and you need to start sending them all the same
commands, then you might find that tools already exist —tools that already provide ways to write command scripts, push them out for execution across a cloud of machines, batch up any error messages or responses for your review, and even save commands
in a queue to be re-tried later in case a machine is down and cannot be reached at the moment.

What are the options?
First, the [Fabric library](https://pypi.python.org/pypi/Fabric/1.9.0) is very popular with Python programmers who need to run commands and
copy files to remote server machines. As you can see in `fabfile.py`, a Fabric script calls very simple
functions with names like `put()`, `cd()`, and `run()` to perform operations on the machines to which it
connects. But you can learn more about it at its web site: http://fabfile.org/.
Although `fabfile.py` is designed to be run by Fabric's own fab command-line tool, Fabric can also
be used from inside your own Python programs; again, consult their documentation for details.

```python


from fabric.api import *

def versions():
    with cd('/usr/bin'):
        with settings(hide('warnings'), warn_only=True):
            for version in '2.4', '2.5', '2.6', '2.7', '3.0', '3.1':
                result = run('python%s -c "None"' % version)
                if not result.failed:
                    print "Host", env.host, "has Python", version

```

Another project to check out is Silver Lining. It is still very
immature, but if you are an experienced programmer who needs its specific capabilities, then you might
find that it solves your problems well. This library goes beyond batching commands across many
different servers: it will actually create and initialize Ubuntu servers through the “libcloud” Python API,
and then install your Python web applications there for you. You can learn more about this promising
project at http://cloudsilverlining.org/.

On the other hand, there is “pexpect.” While it is not, technically, a program that itself knows how to use the
network, it is often used to control the system “ssh” or “telnet” command when a Python programmer
wants to automate interactions with a remote prompt of some kind. This typically takes place in a
situation where no API for a device is available, and commands simply have to be typed each time the
command-line prompt appears. Configuring simple network hardware often requires this kind of clunky
step-by-step interaction. You can learn more about “pexpect” here:
http://pypi.python.org/pypi/pexpect.

Finally, there are more specific projects that provide mechanisms for remote systems
administration. Red Hat and Fedora users might look at func, which uses an SSL-encrypted XML-RPC
service that lets you write Python programs that perform system configuration and maintenance:
https://fedorahosted.org/func/.
