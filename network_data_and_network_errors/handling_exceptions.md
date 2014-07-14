## Handling Exceptions


There are four basic approaches of handling the errors that can occur.

- The first is not to handle exceptions at all. If only you or only other Python programmers will be
using your script, then they will probably not be fazed by seeing an exception.
If you are writing a library of calls to be used by other programmers, then this first approach is
usually preferable, since by letting the exception through you give the programmer using your API the
chance to decide how to present errors to his or her users.


- If you are indeed writing a library, then there is a second approach to consider: wrapping the
network errors in an exception of your own.


- A third approach to exceptions is to wrap a `try…except` clause around every single network call that
you ever make, and print out a pithy error message in its place. While suitable for short programs, this
can become very repetitive when long programs are involved, without necessarily providing that much
more information for the user.


- There is one final reason that might dictate where you add an exception handler to your network
program: you might want to intelligently re-try an operation that failed.
