+++
date = "2016-01-25T20:24:11+01:00"
title = "Selinux"
tags = ["tools", "centos"]
+++

# Selinux

```bash
ls -Z ~/.bashrc
#unconfined_u:object_r:user_home_t:s0
# user:  unconfined_u
# role:  object_r
# type:  user_home_t
# level: s0
```

## states and modes

```bash
getenforce
#Enforcing
```

```bash
setenforce 0
getenforce
#Permissive
```

```bash
setenforce 1
getenforce
#Enforcing
```

# status

```bash
sestatus
```

# semanage

**SELinux Policy Management tool**
```bash
semanage login -l
```

**seinfo - SELinux policy query tool**
```
seinfo -r # list roles
seinfo -b | grep http | grep cgi
```

**SELinux contexts for running processes**
```bash
ps -eZ
```

**SELinux context associated with your Linux user**
```bash
id -Z
```


# chcon

**change file SELinux security context**
```bash
chcon -t samba_share_t /var/www/html/testfile
```

**make http unconfined (not using selinux labels)**
```bash
ls -Z /usr/sbin/httpd
chcon -t unconfined_exec_t /usr/sbin/httpd
ls -Z /usr/sbin/httpd
#restore
restorecon -v /usr/sbin/httpd
```

```bash
useradd newuser 
passwd newuser 
id -Z
userdel -r newuser
```

## config

```bash
vim /etc/selinux/config
```
## booleans

### list

```bash
semanage boolean -l
```

### get

```
getsebool -a
getsebool allow_console_login
getsebool allow_console_login allow_cvs_read_shadow allow_daemons_dump_core
```

### set
```
getsebool httpd_can_network_connect_db
setsebool httpd_can_network_connect_db on
setsebool -P httpd_can_network_connect_db on # make changes persistent across reboots
getsebool httpd_can_network_connect_db
```

## labelig files

```bash
ls -Z file1
```

### chcon
```bash
#changes SELinux context for files (changes do not survive system relabel or restorecon)
chcon -t type file-name 
chcon -R -t type directory-name
chcon -v --type=httpd_sys_content_t /html
chcon -Rv --type=httpd_sys_content_t /html
semanage fcontext -a -t httpd_sys_content_t "/html(/.*)?" 
```

```
ls -Z file1
chcon -t httpd_sys_content_t file1
restorecon -v file1
```

## ports

```bash
semanage port -l | grep http
semanage port -a -t http_port_t -p tcp 9876
```

## audit2allow

**Create Custom SELinux Policy Module**
```bash
grep smtpd_t /var/log/audit/audit.log | audit2allow -m postgreylocal > postgreylocal.te
```
