+++
date = "2016-07-27T20:22:23+02:00"
title = "sed"
tags = "tools"
+++

# sed

```bash
# range of lines
sed -n '5,10p' myfile.txt

# all lines except given range
sed '20,35d' myfile.txt

# non-consecutive lines and ranges
sed -n -e '5,7p' -e '10,13p' myfile.txt

# replace
sed 's/version/story/g' myfile.txt
sed 's/version/story/gi' myfile.txt
ip route show | sed 's/  */ /g'
sed 's/[Zz]ip/rar/g' myfile.txt
sed -i 's/that/this/gi;s/line/verse/gi' myfile.txt

# replace in range
sed '30,40 s/version/story/g' myfile.txt

# remove emty lines or comments (in apache config)
sed '/^#\|^$\| *#/d' httpd.conf

# dos2unix
sed -i 's/\r//' myfile.txt

# in-place editing with backup
sed -i'.orig' 's/this/that/gi' myfile.txt
```
