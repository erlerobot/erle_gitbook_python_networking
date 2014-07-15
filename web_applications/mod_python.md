## mod_python

As it became clear that CGI was both inefficient and inflexible—CGI scripts could not flexibly set the
HTTP return code, for example—it became fashionable to start embedding programming languages
directly in web servers.

 Back in the early days, embedding was also possible, through a somewhat different
approach that actually made Python an extension language for much of the internals of Apache itself.
The module that supported this was `mod_python`, and for years it was by far the most popular way to
connect Python to the World Wide Web.
The `mod_python Apache module put a Python interpreter inside of every worker process spawned by
Apache. Programmers could arrange for their Python code to be invoked by writin. directives into their
Apache configuration.

Today, mod_python is mainly of historical interest. I have outlined its features here, not only because
you might be called upon to maintain or upgrade a service that is still running on mod_python, but
because it still provides unique Apache integration points where Python cannot get involved in any
other way. If you run into either situation, you can find its documentation at http://modpython.org/
