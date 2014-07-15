## Daemons and Logging

A daemon is a computer program that runs as a background process, rather than being under the direct control of an interactive user.
You can also install [python-daemon](https://pypi.python.org/pypi/daemon/1.0) from the Package, and its code will let your server program become a daemon
entirely on its own power.

Another useful thing is the modern logging module, which can write to syslog, files, network sockets, or anything in
between. The simplest pattern is to place something like this at the top of each of your daemon’s source
files:
```python
import logging
log = logging.getLogger(__name__)
```
Then your code can generate messages very simply:
```python
log.error('the system is down')
```
