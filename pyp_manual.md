# Manual

![functions](https://github.com/thepyedpiper/pyp/blob/gh-pages/functions.gif?raw=true)



# Intro
The pyed piper, or pyp, is a command line utility for parsing text output and generating complex unix commands using standard python methods. pyp is powered by python, so any standard python string or list operation is available.

The variable "`p`" represents EACH line of the input, so for example, if p is a string, you can replace all `FOO` with `GOO` using `p.replace('FOO','GOO')`. Likewise, the variable `pp` represents the ENTIRE input as a python list, so to sort the input alphabetically line-by-line, use `pp.sort()`

Standard python relies on whitespace formating such as indentions. Since this is not convenient with command line operations, pyp employs an internal piping structure ("`|`") similar to unix pipes. This allows passing of the output of one command to the input of the next command without nested "(())" structures. It also allows easy spliting and joining of text using simple, commonsense variables (see below). The type of p is the same as the type of the evaluation before the the pipe, so if it evaled to a string, it will stay that way. An added bonus is that any subresult between pipes is available, making it easy to refer to the original input if needed.

Filtering output is straight forward using python Logic operations. Any output that is "True" is kept while anything "False" is eliminated. So `p.isdigit()` will keep all lines that are completely numbers.

The output of pyp has been optimized for typical command line scenarios. For example, if text is broken up into an array using the `split()` method, the output will be conveniently numbered by field because a field selection is anticipated. If the variable `pp` is employed, the output will be numbered line-by-line to facilate picking any particular line or range of lines. In both cases, standard python methods `list[start:end]` can be used to select fields or lines of interest. Also, the standard python string and list objects have been overloaded with commonly used methods and attributes. For example, `pp.uniq()` returns all unique members in an array, will `p.kill('foo')` will eliminate all "foo" in the input.

pyp commands can be easily saved to disk and recalled using user-defined macros, so a complicated parsing operation requiring 20 or more steps can be recalled easily, providing an alternative to quick and dirty scripts. For more advanced users, these macros can be saved to central location, allowing other users to execute them. Also, an additional text file (`PypCustom.py`) can be set up that allows additional methods to be added to the pyp str and list methods, allowing tight integration with larger facilities data structures or custom tool sets.

# DESIGN PHILOSOPHY
## Python Linearization
Pyp was designed to merge common python methods with classic unix piping techniques. Originally, the primary goal was simply to eliminate nested parentheses necessary to perform a sequence of python string operations on STD-IN. The beauty of classic unix tools like cut, sed, and awk, is that each operation performs a singular, comprehensible operation whose output is then piped to the next tool. Python, on the other hand, was designed as a whitespace-delimited language where it's trivial to make temporary variables, split out functions, and other such housekeeping functions. It's possible to cleverly devise a series of nested parentheses to get around this, but at the expense of human readability. For example, compare these two equivalent statements which represent a typical command line parsing task:

Here is the traditional python implementation:

`'X'.join('_'.join(line.split()[4:7]).split('.'))`

Now, consider how you could do things if you could pass the output of one python evaluation to the next linearly using the variable p:

`line.split() | p[4:7] | '_'.join(p) | p.split('.') | 'X'.join(p)`

Hopefully, you'll agree that the second one is easier to write, read, and especially, read in a week or a year! This basic generalization opened up a number of techniques which are detailed below.

## Normal and Analysis Output Modes
In use, pyp is designed to alternate between two modes: a normal output mode as well as an analysis mode used to help make informed decisions. For example, if you use the split() method, pyp knows that you are probably interested in selecting fields or some other intermediate step, so it prints out index numbers to facilitate field selection:

`ls random_frame.jpg | pyp "p.split('.')"`

`[[0]random_frame[1]jpg]`

## Simple Object Printing
With this in mind, pyp, unlike normal python, allows you to print out any type of objects next to each other without converting them to strings. That is, you can use "+" and "," to concatenate objects or join them with spaces. For example you can print out a string right next to an indexed list:

`ls random_frame.jpg | pyp "'hello world', p.split('.')"`

`hello world [[0]random_frame[1]jpg]`

...then select your field of interest, and keep on going with the now canonical string. The overall goal is to keep you thinking in a pythonic way while giving you as much information as possible about what you have.

## Pyp Tenets
* python experts should be able to do everything with two variables `p` (line by line objects) and `pp` (entire input as list)

* most splitting and joining happens on less than 10 metacharacters. these should have commonsense dedicated variables.

* `split/join` variables should be aware of the input type. ie slash splits on slashes if the line is a string, but joins with slashes if the line is a list.

* python bouleon logic should be hijacked to filter output, `True` is kept, `False` is lost.

* you should be able to easily refer back to any inter-pipe sub-result, especially the original input that was piped into pyp

* * once a process is worked out, you should be able to save it to disk for future use as a macro-- an alternative to quick and dirty scripting.

* * easily extensible string and list objects to add funtionality beyond normal python methods both in main code and user config file.

## SYSTEM REQUIREMENTS
Pyp needs a copy of python to work. Any recent (version 2.7+) should work. We have purposefully only imported commonly distributed modules to maximize compatibility. Older versions of python may work if the json package has been installed. Pyp has been successfully tested on fedora, centOS, and Mac OSX systems.

## INSTALLATION
The simplest installation method is to simply download pyp from our "Downloads" page. Keep in mind that pyp is a single, very small text file, not a complex package, so can be simply downloaded and run, although you may need to change permissions.

## Downloads

There is a beta version that typically has expanded features and bug fixes here as well.

In either case, you may need to change the permission on the file to be 777 (read/write/execute all). This is easily done with the following command:

`chmod 777 pyp*`

If you don't have permissions to change permissions, you can try this and provide your password when prompted:

`sudo chmod 777 pyp*`

If installing into a directory outside of your scripts path on Mac OSX, you must give path information to execute pyp, for example, you can call pyp like this: 

`ls | ./pyp "p" `

pyp is available from other sources like pypy, but the beta version is only available from the "Downloads" page. Downloading from this page has proven to be the most reliable method of installing pyp.

## STRING OPERATIONS
Here is a simple example for splitting the output of "ls" (unix file list) on dots '.':

`ls random_frame.jpg | pyp "p.split('.')"`

`==> [[0] random_frame[1]jpg]`

The variable "p" represents each line piped in from "ls". Notice the output has index numbers, so it's trivial to pick a particular field or range of fields, i.e. pyp "p.split('.')[0]" is the FIRST field. There are some pyp generated variables that make this simpler, for example the variable "d" or "dot" is the same as p.split('.'):

`ls random_frame.jpg | pyp "d"`

`==>[[0] random_frame[1]jpg]`

`ls random_frame.jpg | pyp "d[0]"`

`==> random_frame`

To Join lists back together, just pipe them to the same or another built in variable(in this case "u", or "underscore"):

`ls random_frame.jpg | pyp "d"`

`==> [[0]random_frame[1]jpg]`

`ls random_frame.jpg | pyp "d|underscore"`

`==> random_frame_jpg`

To add text, just enclose it in quotes, and use "+" or ",`" just like python:

`ls random_frame.jpg | pyp "'mkdir','seq.tp_' + d[0]+ '_v1/misc_vd8'"`

`==> mkdir seq.tp_random_frame_v1/misc_vd8`

## LIST OPERATIONS
To perform operations that operate on the ENTIRE array of inputs, Use the variable "pp", which you can manipulate using any standard python list methods. For example, to sort the input, use:

`pyp "pp.sort()"`

When in array context, each line will be numbered with it's index in the array, so it's easy to, for example select the 6th line of input by using:

`pyp "pp[5]"`

You can pipe this back to p to continue modifying this input. There are several methods that have been added to facilitate complex operations for these inputs such as keeping unique members or compressing the entire list to one line (pp.uniq(), and pp.oneline() ...see below).

## MATH OPERATIONS
To perform simple math, use the integer or float functions (int() or float()) and put the math in "()"

`echo 776 | pyp "(int(p) + 1)"`

`==> 777`

## LOGIC FILTERS
To filter output based on a python function that returns a Booleon (True or False)...just pipe the input to this function, and all lines that return True will keep their current value, while all lines that return False will be eliminated. '

`echo 777 | pyp "p.isdigit()"`

`==> 777`

Keep in mind, that if the Boolean is True, the entire value of p is returned. This comes in handy when you want to test on one field, but use something else. For Example, a[2].isdigit() will return p, not a[2] if a[2] is a digit.

Standard python logic operators such as "and","or","not", and 'in' work as well.

For example to filter output based on the presence of "GOO" in the line, use this:

`echo GOO | pyp "'G' in p"`

`==> GOO`

The pyp functions `keep(STR)` and `lose(STR)`, and their respective shortcuts, `k(STR)` and `i(STR)`, are very useful for proving simple OR style string filtering. See Below.

if you are using a math expression, be sure to enclose this in parentheses like this:

`(x > 5)`

In fact, any expression that returns a numeric result (ie float or int) should be enclosed with parentheses. This is one of the rare cases where pyp syntax deviates slightly from normal python.

see the math section for more info.

By default False output is eliminated from the final output. If you would like blank lines instead, use the --keep_false flag.

##. SECOND STREAM, TEXT FILE, AND BLANK INPUT
A Quick Review of File and Second Streams
Typically, data is piped into pyp just like any other unix utility through STD-IN. There are, however, two other completely independent ways to get data into pyp. These other streams can then be manipulated separately as well as combined in various ways with the main stream.

File inputs can be specified using the --text_file flag, and are referred to on a line-by line-basis using the variable 'fp'. For example, to print out the std-in line-by-line next to each line in a file use this:


`pyp "p + fp" --text_file example.txt`


This setup is geared mostly towards combining data from std-in with that in a text file...if the text file is your only data, you should cat it, and pipe this into pyp.

Second stream inputs, on the other hand, are anything after the pyp command that is not associated with an option flag. This can then be accessed separately from the primary stream by using the variable 'sp'. To print out the std-in line-by-line next to each sp_example, use this:



`pyp "p + sp" sp_example1 sp_example2 sp_example3`


## File and Second Stream List Variables
List operations can be performed on file inputs and second stream inputs using the variables spp and fpp, respectively. These essentially treat each input as a list, so you can use standard python list methods as well as any of the specialized pyp list methods. Once modified, these changes will "stick", so future references to the list will use the new list.

For example to sort a file input use: ```

`fpp.sort()`

Once this operation takes place, the sorted fpp will be used for all future operations, such as referring to the file input line-by-line using `fp`.

If you need arbitrary strings added to your list, you can just use simple python list additions:


`fpp + ['last list']`


You can also add these inputs to the std-in stream using like this:


`pp+fpp`


If pp is 10 lines, and fpp is 10 line, this will result in a new pp stream of 20 lines, with the first 10 being from pp and the last 10 being from fpp. fpp will remain untouched; only pp will change with this operation.

Of course, you can trim these to your needs using standard python list selection techniques: pp[0:5]+ fpp[0:5]

This will result in a new composite input stream of 10 lines.

Keep in mind that the length of fpp and spp is trimmed to reflect that of std-in. If you need to see more of your file second stream input, you can extend your std-in stream simply:

`pp+['']*10`


will add 10 blank lines to std-in, and thus reveal another 10 lines of fpp if available.

Also, there are a few useful python math functions that work on lists of integers or floats like sum, min, and max. For example, to add up all of the integers in the last column input: ```

`whitespace[-1] | int(p) | sum(pp)`

If you need to generate output from pyp with no input, use `--blank_inputs`. This is useful for generating text based on line number using the '`n`' variable.

## MACRO USAGE
Macros are a way to permanently store useful commands for future recall. They are stored in your home directory by default. Facilities are provided to store public macros as well, which is useful for sharing complex commands within your work group. Paths to these text files can be reset to anything you choose my modifying the PypCustom.py config file. Macros can become quite complex, and provide a useful intermediate between shell commands and scripts, especially for solving one-off problems. Macro listing, saving, deleting, and searching capabilities are accessible using `--macrolist`, `--macro_save`, `--macro_delete`, `--macro_find` flags. Run `pyp --help` for more details.

you can pyp to and from macros just like any normal pyp command.

`pyp "a[0]| my_favorite_macros | 'ls', p"`

Note, if the macro returns a list, you can access individual elements using [n] syntax:

`pyp "my_list_macro[2]"`

Also, if the macro uses %s, you can append a %(string,..) to then end to string substitute:

`pyp "my_string_substitution_macro%('test','case')"`

By default, macros are saved in your home directory. This can be modifed to any directory by modifying the user_macro_path attribute in your PypCustom.py. If your work in a group, you can also save macros for use by others in a specific location by modifying group_macro_path. See the below section on custom methods about how to set up this file.

## TIPS AND TRICKS
If you have to cut and paste data (from an email for example), execute pyp, paste in your data, then hit CTRL-D...this will put the data into the disk buffer. Then, just rerun pyp with `--rerun`, and you'll be able to access this data for further pyp manipulations!

Using `--rerun` is also great way to buffer data into pyp from long running scripts

pyp is a great way to generate commands before executing them...iteratively keep adding commands until you are confident, then use the `--execute` flag or pipe them to sh. You can use "`;`" to set up dependancies between these commands...which is a great way to work out command sequences that would typically be executed in a "foreach" loop.

break out complex intermediate steps into macros. macros can be run at point in a pyp command.

If you find yourself shelling out constantly to particular commands, it might be worth adding python methods to the PypCustom.py config, especially if you are at a large facility.

## HERE ARE THE BUILT IN VARIABLES:
* `p` line-by-line std-in variable. p represents whatever was evaluated to before the previous pipe (|).
* `pp`  python list of ALL std-in input. In-place methods like sort() will work as well as list methods like sorted(LIST)|
* `sp`  line-by-line input second stream input, like p, but from all non-flag arguments AFTER pyp command: pyp "p, sp" SP1 SP2 SP3 ...| 
* `spp` python list of ALL second stream list input. Modifications of this list will be picked up with future references to sp |
* `fp` line-by-line file input using --text_file TEXT_FILE. fp on the first line of output is the first line of the text file| 
* `fpp` python list of ALL text file input. Modifications of this list will be picked up with future references to fp |
* `original` original line by line input to pyp 
* `o` same as original
* `sp`  second steam line input, just like p, but from all non-flag arguments AFTER pyp
* `quote`  a literal " (double quotes can't be used in a pyp expression)|
* `paran` a literal ' 
* `dollar` a literal $ 
* `n` line counter (1st line is 0, 2nd line is 1,...use the form "(n+3)" to modify this value. 
* `nk`  n + 1000 
* `date` date and time. Returns the current datetime.datetime.now() object.
* `pwd` | present working directory | 
* `history` | history array of all previous results: so pyp "a|u|s|i|h[-3]" shows eval of s 
* `h` same as history
* `letters` abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ 
* `digits` 0123456789 
* `punctuation`  !"#$%&'()*+,-./:;<=>?@[\]^_{|}~` |

## SPLIT OR JOIN VARIABLES BASED ON p BEING A STRING OR LIST
*  `s` OR `slash` p split/joined on "/" 
*  `d` OR `dot` p split/joined on "." 
*  `w` OR `whitespace` p split on whitespace (on spaces,tabs,etc), joined on spaces
*  `u` OR `underscore`  p split/joined on '_' 
*  `c` OR `colon` p split/joined on ':' 
*  `mm` OR `comma` p split/joined on ',' 
*  `m` OR `minus` p split/joined on '-' 
*  `a` OR `all` p split on [' '-_=$...] (on "All" metacharacters)

Also, the ORIGINAL INPUT line is split on delimiters as above, but stored in os,od,oa,ou,ol,om and oe

## HERE ARE THE BUILT IN FUNCTIONS AND ATTRIBUTES:
### p STRING (all STRING methods like p.replace(STRING1,STRING2) work
* `p.dir` path DIRECTORY
* `p.file`  path FILE 
* `p.ext` path EXTENSION 

* `p.trim()` removes last file or directory from path from p p.clean(DELIM)|replaces "bad" metacharacters with underscores or DELIM
* `p.kill(STR1,STR2,..)` removes all STR* from p 
* `p.letters()` returns array of contiguous letters in p 
* `p.digits()` returns array of contiguous numbers in p. 
* `p.punctuation()` returns array of contiguous punctuation in p 
* `p.re(REGEX)` returns portion of string that matches REGEX regular expression. works great with p.replace(p.re(REGEX),STR 

### pp LIST (all LIST methods like pp.sort(), pp[-1], and pp.reverse() work
* `pp.delimit(DELIM)` split input on delimiter instead of newlines pp.divide(N) | consolidates N consecutive lines to 1 line. 
* `pp.before(STRING, N)` searches for STRING, colsolidates N lines BEFORE it to the same line. Default is 1
* `pp.after(STRING, N)` searches for STRING, colsolidates N lines AFTER it to same line. Default is 1.
* `pp.matrix(STRING, N)` returns pp.before(STRING, N) and pp.after(STRING, N). Default is 1. 

* `pp.uniq()` returns only unique elements
* `pp.unlist()` breaks up ALL arrays up into seperate single lines
* `pp.oneline()` combines all list elements to one line with spaces

## NATIVE PYP FUNCTIONS
* `keep(STR1,STR2,...)` keep all lines that have at least one STRING in them
* `k(STR1,STR2,...)` shortcut for keep(STR1,STR2,...) 
* `lose(STR1,STR2,...) `lose all lines that have at least one STRING in them 
* `l(STR1,STR2,...)` shortcut for lose(STR1,STR2,...) 

* `rekeep(REGEX) `keep all lines that match REGEX regular expression
* `rek(REGEX)` shortcut for rekeep(REGEX) 
* `relose(REGEX)` lose all lines that match REGEX regular expression
* `rel(REGEX)` shortcut for relose(REGEX) 

* `shell(SCRIPT)` shell('stat '+p) returns output of $script
* `shelld(SCRIPT,DELIM)` shelld('stat '+p) | returns output of $script in dictionary key/value seperated on ':' or delimeter
* `env(ENVIROMENT_VAR)` returns value of evironment variable using os.environ.get()
* `glob(PATH)` returns globed files/directories at PATH. Make sure to use '*' wildcard

##CUSTOM METHODS
pyed pyper relies on overloading the standard python string and list objects with it's own custom methods. If you'd like to try writing your own methods either to simplify a common task or integrate custom functions using a propietary API, it's straightforward to do. You'll have to setup a config file first:

`pyp --unmodified_config > PypCustom.py`

`sudo chmod 777 PypCustom.py`

There are example functions for string, list, powerpipe, and generic methods. to get you started. When pyp runs, it looks for this text file and automatically loads any found functions, overloading them into the appropriate objects. You can then using your custom methods just like any other pyp function.

SIMPLE EXAMPLES:
* `pyp "'foo ' + p"  ==> "foo" + current line`
* `pyp "p.replace('x','y') | p + o" ==> current line w/replacement + original line `
* `pyp "p.split(':')[0]" ==> first field of string split on ':'`
* `pyp "slash[1:3]" ==> array of fields 1 and 2 of string split on '/'`
* `pyp "s[1:3]|s"  ==> string of above joined with '/'`
