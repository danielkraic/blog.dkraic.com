+++
date = "2015-11-12T19:41:10+01:00"
draft = true
title = "grep"

+++

# Grep

```
-i Ignore case
-v Invert match
-c Count of matches
-l File names only
-n Lines numbers
-h No file name
-f Regex from file
-P Use Perl regex
-A num
-B num
-r Search recursively
```

grep:
```
*
.
^ $
[...] [^...]
\n
\(...\)
\{i\} \{i,j\} \{i,\}
```

GNU grep:
```
\? \+
\b \B
\w \W
\s \S
```
grep -E:
```
+ ? |
() {i} {i,j} {i,}
[:alpha:] space digit lower upper
```
grep -P:
```
\d \D \A \Z
```
