+++
date = "2015-09-14T20:06:13+02:00"
title = "Golang installation with GVM"
tags = [ "dev", "golang" ]
+++

[GVM](https://github.com/moovweb/gvm) is version manager for [golang](https://golang.org/). GVM provides an interface to install and manage multiple *golang* versions.

### Install GVM

```bash
bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
```

### List available versions of golang
```bash
gvm listall
```

### Install version v1.4
```bash
gvm install go1.4
```

### Use v1.4 (as default)
```bash
gvm use go1.4 --default
```
