+++
date = "2015-10-30T18:08:13+01:00"
title = "DNF"
tags = [ "sys", "fedora", "dnf" ]
+++
# DNF

* package manager in Fedora
* DNF - Dandified Yum
* next generation YUM
* docs: http://dnf.readthedocs.org/en/latest/

|command|description|
|----|-------|
|dnf help|help|
|dnf install vim|install pkg|
|dnf reinstall vim|reinstall pkg|
|dnf remove vim # or erase|remove pkg|
|dnf check-update|check system updates|
|dnf update vim|update pkg|
|dnf update # or upgrade|update all pkgs|
|dnf search vim|search pkg|
|dnf info|show pkg info|
|dnf provides /bin/bash|find pks which provides file|
|dnf list installed|list all installed pkgs|
|dnf list available|list all available pkgs|
|dnf autoremove|remove no-longer-needed pkgs|
|dnf clean all|clean caches|
|dnf history|view history|
|dnf repolist|list all enabled repos|
|dnf repolist all|list all enabled ans disabled repos|
|dnf grouplist|list pkg groups|
|dnf groupinstall 'LibreOffice'|install pkg group|
|dnf groupupdate 'LibreOffice'|update pkg group|
|dnf groupremove 'LibreOffice'|remove pkg group|
|dnf --enablerepo=epel install phpmyadmin|install pkg form specific repo|
|dnf distro-sync|sync pkgs to stable release|
|dnf copr enable user/project |enable copr repo|
|dnf copr disable user/project |disable copr repo|
