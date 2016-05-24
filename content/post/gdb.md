+++
date = "2016-05-24T10:30:00+02:00"
title = "GDB"
tags = [ "dev", "cpp" ]
+++

http://www.root.cz/clanky/trasovani-a-ladeni-nativnich-aplikaci-v-linuxu-pouziti-gdb-a-jeho-nadstaveb/

```
gdb program
gdb program core
gdb program 1234

help

q

shell date
!date

set logging on
set logging off
set logging file file # default logfile is gdb.txt.

info program
info functions
info sources
info sharedlibrary

list
list main
list 1,12

search regex
reverse-search regex

run

break main
clear main
info breakpoints

n
c

print(i)
print(argc>0)
print(atoi("42"))
print(isdigit('4'))
print(isdigit('a'))

bt

ulimit -c unlimited
./npe
Segmentation fault (core dumped)

gdb npe core
```
