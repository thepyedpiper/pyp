# Legacy Equivalents

There are multiple pyp solutions listed for legacy commands, starting with canonical python and then including increasing levels of shortcuts.  Please use whatever makes sense to you.  The pyp [manual](https://thepyedpiper.github.io/pyp/pyp_manual.html) has a full list of shortcuts and special functions.  Remember, `p` represents every line as a python `string` object, while `pp` represents the entire input as a python `list` object.

| LEGACY       | PYP            | NOTES                                      |
|:-------------|:------------------|:-------------------------------------------|
| awk '{print $1}'| p.split()[0] |splits line on `whitespace`, selects first field|
| awk '{print $1}'| whitespace[0] | `whitespace` is array of line split on `whitespace`|
| awk '{print $1}'| w[0] | `w` is shortcut for whitespace|
| awk '{print $3 $2 $1}'| w[2],w[1],w[0] | you can use `+` instead of `,` for no spaces|
| cut -d / -f 3 | p.split('/')[2] | standard python technique |
| cut -d / -f 3 |slash[2] |`slash`  is array of line split on `/` |
| cut -d / -f 3 | s[2] | `s` is shortcut for `slash`|
|expr 1 + 1 | (1 + 1) | you must use parantheses for math ops |
| grep X      | keep("X")         |      standard `grep`                                      |
| grep X      | k("X")        | shortcut `grep` notation |
| grep -E "l.*y" | rekeep('l.*y') | `grep` with regex | 
| grep X -B 1 | pp.before('X',1) | keeps the line before the one grepped | 
| grep -v X    | lose("X")        | reverse `grep` |
| grep -v X    | l("X")        | shortcut reverse `grep` notation|
| grep -v -E "l.*y" | relose('l.*y') | reverse `grep` with regex |
| head 10 | pp[0:9] |standard python list notation|
|sed s/legacy/pyp/ | p.replace('legacy','pyp') | standard python string method|
|sed s/@// |p.replace('@','')| standard python string method|
|sed s/@// | p.kill('@') | custom pyp string function| 
|sort | pp.sort()|standard pyp list method|
|sort -u| pp.uniq()| custom pyp funtion|
|tail 10| pp[-10:]|standard python list notation| 
|xargs | pp.oneline() | custom pyp function that makes list one line |
|???| p.ext | returns just the file extension |
|???| p.file | returns just the file name |
|???| p.dir | returns just the directory |
