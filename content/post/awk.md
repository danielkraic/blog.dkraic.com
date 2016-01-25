+++
date = "2016-01-19T22:43:51+01:00"
title = "AWK"
tags = "tools"
+++

# AWK

```bash
awk '$2 > $3 {print $1," - ",$2,":",$3}' file
```

```bash
awk -f file.awk in_file
```

print $1 " :: " $2
print $1," :: ",$2
printf("%s - $s", $1, $2)


