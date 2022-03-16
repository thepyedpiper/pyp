logo: "https://opensource.imageworks.com/images/large/pyp.jpg"

# Piping Python Through Pipes


`ls | pyp "p.replace('maybe','yes') | pp.sort() | pp[1:3] |p , p , p.strip('abc') | whitespace | p[3], 'no'"`

Pyp is a linux command line text manipulation tool similar to awk or sed, but which uses standard python string and list methods as well as custom functions evolved to generate fast results in an intense production environment.Pyed Pyper was developed at Sony Pictures Imageworks to facilitate the construction of complex image manipulation "one-liner" commands and has been used for visual effects work on Spiderverse, The Meg, and the Mitchell Vs The Machines.  Although originally released in 2012 with the technical needs of Visual Effects in mind, pyp has been used successfully in a wide range of fields where a quick, ad hoc programming environment is needed including particle physics, bioninformatics and espionage.

   Text parsing and manipulations techniques in formal programming settings have made incredible strides since the 1970s and 1980s, but unfortunately this hasn't been reflected when directly interacting with UNIX terminal, which still reigns supreme in many technical settings.  Pyp aims fill this gap by consolidating the plethora of powerful but painfully idiosyncratic "classic" commands (awk, sed, grep, tr, perl -p, etc) into rational and human readable python based statements.  Because of this, pyp has appeared on the sylibi of major educational institutions such as [Stanford](https://web.stanford.edu/class/physics91SI/cgi-bin/?page_id=317) as a modern alternative to legacy techniques.

At it's core, pyp's function can be reduced to three basic principles:

* the variable p represesnts every line of the input as a python string object: `p.split(':')`
* the variable pp represents entire input as a python list object: `pp.sort()` 
* the results of either of these be internally piped `"|"` to another pyp command `pp.sort()|p.split(':')`

These three principles are just the basic pyp underpinnings: they allow you to use every python string and list function natively while maintaing the classic UNIX pipe workflow.  Pyp also overloads numerous new list and string functions that replicate almost all legacy UNIX commands.  For example `p.keep()` works just like grep while `pp.uniq()` works just like `sort -u.` 

Because pyp employs it's own internal piping syntax (`"|"`) similar to unix pipes, complex operations can be proceduralized by feeding the output of one python command to the input of the next. This greatly simplifies the generation and troubleshooting of multistep operations without the use of temporary variables or nested parentheses. In practice, the ability to easily construct complicated command sequences can largely replace "for each" loops on the command line, thus significantly speeding up work-flow using standard unix command recycling.



pyp output has been optimized for typical production scenarios. For example, if text is broken up into an array using the `split()` method, the output will be automatically numbered by field making selecting a particular field trivial.

`ls random_frame.jpg | pyp "p.split('.')`

`[[0]random_frame[1]jpg]` 
 

Numerous other conveniences have been included, such as an accessible history of all inter-pipe sub-results, an ability to perform mathematical operations, and a complement of variables based on common metacharcter `split/join` operations.

For power users, commands can be easily saved and recalled from disk as `macros`, providing an alternative to quick and dirty scripting. For the truly advanced user, additional methods can be added to the pyp class via a config file, allowing tight integration with larger facilities data structures or custom toolsets.





# A Quick Tour
The simplest pyp example shows how python string methods can be used easily on the command line. For example, to split up the different columns of a linux long listing, we just use the split method with pyp's line-by-line variable "p"

`ls -l | pyp "p.split()"`

we can then use standard python indexing to select the column. For example, to select the last column, we can just use this:

`ls -l | pyp "p.split()[-1]"`

Any other python string methods can be used; for example p.lower() will make everything lowercase.

For a more complicated example, we take a linux long listing, capture every other of the 5th through the 10th lines, keep username and file name fields, replace "hello" with "goodbye", capitalize the first letter of every word, and then add the text "is splendid" to the end:

`ls -l | pyp "pp[5:11:2] | whitespace[2], w[-1] | p.replace('hello','goodbye') | p.title(),'is splendid'"`

This uses pyp's built-in line-by-line and entire input variables (p and pp), as well as the variable whitespace and it's shortcut w, which both represent a list based on splitting each line on whitespace (whitespace = w = p.split()).

The other functions and selection techniques are all standard python. Notice the pipes (`"|"`) are inside the pyp command.

We can then save this as a macro to disk using the flag `--macro_save splendid_example`

The next time we need to perform this operation, we can simply use this:

`ls -l | pyp splendid_example`







