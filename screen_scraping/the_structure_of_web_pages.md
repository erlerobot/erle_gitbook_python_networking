## The Structure of Web Pages

The Hypertext Markup Language (HTML) is one of many markup dialects built atop the Standard
Generalized Markup Language (SGML), which bequeathed to the world the idea of using thousands of
angle brackets to mark up plain text. Inserting bold and italics into a format like HTML is as simple as
typing eight angle brackets:
- `
The <b>very</b> strange book <i>Tristram Shandy</i>.`
The <b>very</b> strange book <i>Tristram Shandy</i>.

In the terminology of SGML, the strings `<b`> and `</b>` are each tags—they are, in fact, an opening
and a closing tag—and together they create an element that contains the text very inside it. Elements
can contain text as well as other elements, and can define a series of key/value attribute pairs that give
more information about the element:
- `<p content="personal">I am reading <i document="play">Hamlet</i>.</p>`
<p content="personal">I am reading <i document="play">Hamlet</i>.</p>

The problem with SGML
languages in this regard—and HTML is one particular example—is that they expect parsers to know the
rules about which elements can be nested inside which other elements, and this leads to constructions
like this unordered list` <ul`>, inside which are several list items `<li>`:

- `<ul><li>First<li>Second<li>Third<li>Fourth</ul>`
<ul><li>First<li>Second<li>Third<li>Fourth</ul>

Since HTML in fact says that <li> elements cannot
nest, an HTML parser will understand the foregoing snippet to be equivalent to this more explicit XML
string:

- `<ul><li>First</li><li>Second</li><li>Third</li><li>Fourth</li></ul>`
<ul><li>First</li><li>Second</li><li>Third</li><li>Fourth</li></ul>

And beyond this implicit understanding of HTML that a parser must possess are the twin problems
that, first, various browsers over the years have varied wildly in how well they can reconstruct the
document structure when given very concise or even deeply broken HTML; and, second, most web page
authors judge the quality of their HTML by whether their browser of choice renders it correctly. This has
resulted not only in a World Wide Web that is full of sites with invalid and broken HTML markup, but also in the fact that the permissiveness built into browsers has encouraged different flavors of broken
HTML among their different user groups.

For more documentation about these topic visit:

- http://www.w3.org/MarkUp/Guide/
- http://www.w3.org/MarkUp/Guide/Advanced.html
- http://www.w3.org/MarkUp/Guide/Style
- http://werbach.com/barebones/barebones.html
- http://www.w3.org/TR/REC-html40/
- http://validator.w3.org/
- http://tidy.sourceforge.net/

