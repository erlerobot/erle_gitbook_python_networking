## Templates

Almost all web frameworks expect you to produce web pages by combining Python code called a view
with an HTML template; you saw this approach in action in `wsgi_app.py`. This approach has gained
traction because of its eminent maintainability: building a dictionary of information is best performed in
plain Python code, and the items fetched and arranged by the view can then easily be included by the
template, so long as the template language supports basic actions like iteration and some form of
expression evaluation.
(A template is a document consisting of rows and tables, with different ranges and sizes, which facilitates the development of web pages, letters or other content).
It is one of the glories of Python that we use views and templates, and one of the
shames of traditional PHP development that developers would freely intermix HTML and extensive PHP
code to produce a single, unified mess.

Views can also become more testable when their only job is to generate a dictionary of data. A good
framework will let you write tests that simply check the raw data returned by the function instead of
making you peek repeatedly into fully rendered templates to see if the view corralled its data correctly.

There seem to be two major differences of opinion among the designers and users of the various
template languages about what constitutes the best way to use templates:

- Should templates be valid HTML with iteration and expressions hidden in element
attributes? Or should the template language use its own style of markup that
festoons and wraps the literal HTML of the web page? While the
former can let the developer run HTML validation against template files before
they are ever rendered and be assured that rendering will not change the
validator's verdict, most developers seem to find the latter approach much easier
to read and maintain.


- Should templates allow arbitrary Python expressions in template code, or lock
down the available options to primitive operations like dictionary get-item and
object get-attribute? Many popular frameworks choose the latter option, requiring
even lazy programmers to push complex operations into their Python code
“where it belongs.” But several template languages reason that, if Python
programmers do so well without type checking, then maybe they should also be
trusted with the choice of which expressions belong in the view and which in the
template.

Since many Python frameworks let you plug in your template language of choice, and only a few of
them lock you down to one option, you might find that you can pair your favorite approaches.
