## Command-Line Expansion and Quoting


If you have ever typed many commands at a Unix command prompt, you will be aware that not every
character you type is interpreted literally.
Consider this command, for example:
```
root@erlerobot:~# echo *
Hello.txt Python-3.4.1 Python-3.4.1.tgz Python_files build gmapenv hola.txt otro text.txt virtualenv-1.11.6 virtualenv-1.11.6.tar.gz
root@erlerobot:~#
```
The asterisk * in this command was not interpreted to mean “print out an asterisk character to the
screen”; instead, the shell thought I was trying to write a pattern that would match all of the file names in
the current directory. To actually print out an asterisk, I have to use another special character—an
“escape” character, because it lets me “escape” from the shell's normal meaning—to tell it that I just
mean the asterisk literally:
```
root@erlerobot:~# echo Here is a lone asterisk: \*
Here is a lone asterisk: *
root@erlerobot:~# echo And here are '*' two "*" more asterisks
And here are * two * more asterisks
root@erlerobot:~#

```
The rules by which modern shells interpret the special characters in
your command line have become quite complex.
Instead, to use the command line effectively, you just have to understand two points:

- Special characters are interpreted as special by the shell you are using, like bash.


- When passing commands to a shell either locally or across the network, you need to escape the special characters you
use so that they are not expanded into unintended values on the remote system.


