+++
date = "2015-09-16T20:54:52+02:00"
title = "Ansible"
tags = [ "dev", "ansible" ]
+++
### Installation

#### Install from PPA

```bash
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible
```

#### Install with pip

```bash
sudo pip install ansible
```

### Configuration

#### Add localhost to hosts file

```bash
echo "localhost ansible_connection=local" > ~/ansible_hosts
export ANSIBLE_INVENTORY=~/ansible_hosts
```

#### Setup SSH-agent

```bash
ssh-agent bash
ssh-add ~/.ssh/id_rsa
```

#### Hosts file examples

```
[webservers]
www[01:50].example.com

[databases]
db-[a:f].example.com

[targets]
localhost              ansible_connection=local
other1.example.com     ansible_connection=ssh        ansible_ssh_user=mpdehaan
other2.example.com     ansible_connection=ssh        ansible_ssh_user=mdehaan
```

### Ad-Hoc Commands

#### Ping all hosts

```bash
# ping
ansible all -m ping
# ask ssh password
ansible all -m ping --ask-pass
# as bruce
$ ansible all -m ping -u bruce
# as bruce, sudoing to root
$ ansible all -m ping -u bruce --sudo
# as bruce, sudoing to batman
$ ansible all -m ping -u bruce --sudo --sudo-user batman
```

#### Run command

```bash
ansible localhost -a "/bin/echo hello"
ansible localhost -a "uname -a"
ansible localhost -m shell -a 'echo $TERM'
```

#### Run command in parallel

```bash
ansible atlanta -a "/sbin/reboot" -f 10
```

#### Run as other user with sudo

```bash
ansible atlanta -a "/usr/bin/foo" -u username --sudo [--ask-sudo-pass]
```

#### File transfer

```bash
# create file
ansible atlanta -m copy -a "src=/etc/hosts dest=/tmp/hosts"
# create file and set permissions
ansible webservers -m file -a "dest=/srv/foo/b.txt mode=600 owner=mdehaan group=mdehaan"
# create directory
ansible webservers -m file -a "dest=/path/to/c mode=755 owner=mdehaan group=mdehaan state=directory"
# delete file
ansible webservers -m file -a "dest=/path/to/c state=absent"
```
#### Git

```bash
ansible webservers -m git -a "repo=git://foo.example.org/repo.git dest=/srv/myapp version=HEAD"
```

#### Services

```bash
ansible webservers -m service -a "name=httpd state=started"
ansible webservers -m service -a "name=httpd state=stopped"
ansible webservers -m service -a "name=httpd state=restarted"
```

### Playbooks

#### Examples

```yaml
---
- hosts: webservers
  vars:
    http_port: 80
    max_clients: 200
  remote_user: root
  tasks:
  - name: ensure apache is at the latest version
    yum: pkg=httpd state=latest
  - name: write the apache config file
    template: src=/srv/httpd.j2 dest=/etc/httpd.conf
    notify:
    - restart apache
  - name: ensure apache is running (and enable it at boot)
    service: name=httpd state=started enabled=yes
  handlers:
    - name: restart apache
      service: name=httpd state=restarted
```


```yaml
---
- hosts: webservers
  vars:
    http_port: 80
    max_clients: 200
  remote_user: root
  tasks:
  - name: ensure apache is at the latest version
    yum:
      pkg: httpd
      state: latest
  - name: write the apache config file
    template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf
    notify:
    - restart apache
  - name: ensure apache is running
    service:
      name: httpd
      state: started
  handlers:
    - name: restart apache
      service:
        name: httpd
        state: restarted
```

```yaml
---
- hosts: webservers
  remote_user: root

  tasks:
  - name: ensure apache is at the latest version
    yum: pkg=httpd state=latest
  - name: write the apache config file
    template: src=/srv/httpd.j2 dest=/etc/httpd.conf

- hosts: databases
  remote_user: root

  tasks:
  - name: ensure postgresql is at the latest version
    yum: name=postgresql state=latest
  - name: ensure that postgresql is started
    service: name=postgresql state=running
```
