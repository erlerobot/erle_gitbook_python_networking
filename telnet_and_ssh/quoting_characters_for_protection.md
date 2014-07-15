## Quoting Characters for Protection

In the foregoing section, we used routines in Python's [subprocess module](https://docs.python.org/2/library/subprocess.html?highlight=subprocess#subprocess) to directly invoke commands.(The subprocess module allows you to spawn new processes, connect to their input/output/error pipes, and obtain their return codes. )
This was great, and let us pass characters that would have been special to a normal interactive shell. If
you have a big list of file names with spaces and other special characters in them, it can be wonderful to
simply pass them into a subprocess call and have the command on the receiving end understand you
perfectly.

But when you are using remote-shell protocols over the network (which, you will recall, is the
subject of this chapter!), you are generally going to be talking to a shell like bash instead of getting to
invoke commands directly like you do through the subprocess module. This means that remote-shell
protocols will feel more like the s`ystem()` routine from the os module, which does invoke a shell to
interpret your command line, and therefore involves you in all of the complexities of the Unix command
line:
```python
>>> import os
>>> os.system('echo *')
Hello.txt Python-3.4.1 Python-3.4.1.tgz Python_files build gmapenv hola.txt otro text.txt virtualenv-1.11.6 virtualenv-1.11.6.tar.gz
```
Of course, if the other end of a remote-shell connection is using some sort of shell with which you
are unfamiliar, there is little that Python can do. The authors of the Standard Library have no idea how,
say, a Motorola DSL router's Telnet-based command line might handle special characters, or even
whether it pays attention to quotes at all.
But if the other end of a network connection is a standard Unix shell of the sh family, like bash or
zsh, then you are in luck: the fairly obscure Python [pipes module](https://docs.python.org/2/library/pipes.html?highlight=pipes#pipes), which is normally used to build
complex shell command lines, contains a helper function that is perfect for escaping arguments. It is
called quote, and can simply be passed a string:
```python
>>> from pipes import quote
>>> print quote("filename")
filename
'file with spaces'
>>> print quote("file 'single quoted' inside!")
"file 'single quoted' inside!"
>>> print quote("danger!; rm -r *")
'danger!; rm -r *'
```
So preparing a command line for remote execution generally just involves running quote() on each
argument and then pasting the result together with spaces.
Note that using a remote shell with Python does not involve you in the terrors of two levels of shell
quoting! If you have ever tried to build a remote SSH command line that uses fancy quoting, by typing a
local command line into your own shell. The attempt tends to
generate a series of experiments like this:
```
$ echo $HOST
guinness
$ ssh asaph echo $HOST
guinness
$ ssh asaph echo \$HOST
asaph
$ ssh asaph echo \\$HOST
guinness
$ ssh asaph echo \\\$HOST
$HOST
$ ssh asaph echo \\\\$HOST
\guinness
```

using a remote-shell protocol through Python does not involve two levels of shell like
this. Instead, you get to construct a literal string in Python that then directly becomes what is executed
by the remote shell; no local shell is involved.
So if using a shell-within-a-shell has you convinced that passing strings and file names safely to a
remote shell is a very hard problem, relax: no local shell will be involved in our following examples.
