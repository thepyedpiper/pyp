# Legacy Equivalents




| LEGACY       | PYP            | NOTES                                      |
|:-------------|:------------------|:-------------------------------------------|
| awk '{print $1}'| whitespace[0] ||
| awk '{print $1}'| w[0] | w is shortcut for whitespace|
| awk '{print $3 $2 $1}'| w[2],w[1],w[0] ||
| cut -d / -f 3 | slash[2] ||
| cut -d / -f 3 | s[2] | s is shortcut for slash|
| grep X       | keep("X")         |                                            |
| grep -v X    | lose("X")         | |
|sed s/@// | p.replace('@','') | standard python string method|
|sed s/@// | p.kill('@') | custom pyp string function| 
| sort | pp.sort()|standard pyp list method|
|sort -u| pp.uniq()| custom pyp funtion|
|xargs | pp.oneline() | custom pyp function that makes single line\
