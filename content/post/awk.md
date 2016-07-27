+++
date = "2016-01-19T22:43:51+01:00"
title = "AWK"
tags = "tools"
+++

# AWK

* `FILENAME` : current input file name( do not change variable name)
* `FR` : number of the current input line (that is input line 1, 2, 3… so on, do not change variable name)
* `NF` : number of fields in current input line (do not change variable name)
* `OFS` : output field separator
* `FS` : input field separator
* `ORS` : output record separator
* `RS` : input record separator

```bash
# NR, NF
awk ' END { print "Number of records in file is: ", NR } ' file.txt
awk '{ print "Record:",NR,"has",NF,"fields" ; }' file.txt
```

```bash
# FS
awk -F':' '{ print $1, $4 ;}' /etc/passwd
awk ' BEGIN {  FS=“:” ; }  { print $1, $4  ; } ' /etc/passwd
awk -F':' ' BEGIN { OFS="==>" ;} { print $1, $4 ;}' /etc/passwd
```

```bash
# regex
awk '/localhost/{print}' /etc/hosts
awk '/l.c/{print}' /etc/hosts
awk '/l*c/{print}' /etc/hosts
awk '/[0-9]/{print}' /etc/hosts
awk '/^fe/{print}' /etc/hosts
awk '/ab$/{print}' /etc/hosts
awk '/\$25.00/{print}' deals.txt
```

```bash
# print
awk '{print $1 $2 $3 }' file.txt
awk '{print $1, $2, $3; }' file.txt
awk '{printf "%-10s %s\n",$2, $3 }' file.txt
awk '/^..$/ {print "DVA", $0} /^...$/ { print "TRI", $0}' file.txt
```

```bash
# operators
awk '$3 <= 30 { print}' file.txt
awk '$3 <= 30 && $3 != 0 { print}' file.txt
awk '$3 <= 30 { print "small" } $3 > 30 { print "large" }' file.txt
```

```bash
awk -f file.awk in_file
```
