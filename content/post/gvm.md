+++
date = "2015-09-14T20:06:13+02:00"
title = "Golang installation with GVM"
tags = [ "dev", "golang" ]
+++
GVM is Go Version Manager ([link](https://github.com/moovweb/gvm)). GVM provides an interface to manage Go versions.

### Install GWM

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

### Use v1.4
```bash
gvm use go1.4 --default
```
