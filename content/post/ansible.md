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

#### [Inventory](http://docs.ansible.com/ansible/intro_inventory.html)

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

### [Ad-Hoc Commands](http://docs.ansible.com/ansible/intro_adhoc.html)

#### [Patterns](http://docs.ansible.com/ansible/intro_patterns.html)

```bash
ansible <pattern_goes_here> -m <module_name> -a <arguments>
```

#### Ping hosts

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

#### [File](http://docs.ansible.com/ansible/file_module.html)

```bash
# create file and set permissions
ansible webservers -m file -a "dest=/srv/foo/b.txt mode=600 owner=mdehaan group=mdehaan"
# create directory
ansible webservers -m file -a "dest=/path/to/c mode=755 owner=mdehaan group=mdehaan state=directory"
# delete file
ansible webservers -m file -a "dest=/path/to/c state=absent"
```

#### [Copy](http://docs.ansible.com/ansible/copy_module.html)

```bash
# copy file
ansible atlanta -m copy -a "src=/etc/hosts dest=/tmp/hosts"
ansible atlanta -m copy -a "src=/srv/myfiles/foo.conf dest=/etc/foo.conf owner=foo group=foo mode=0644 backup=yes"
ansible atlanta -m copy -a "src=/mine/sudoers dest=/etc/sudoers validate='visudo -cf %s'"
```

#### [Fetch](http://docs.ansible.com/ansible/fetch_module.html)

```bash
# fetch file from remote node
ansible atlanta -m fetch -a "src=/tmp/somefile dest=/tmp/fetched"
ansible atlanta -m fetch -a "src=/tmp/somefile dest=/tmp/prefix-{{ ansible_hostname }} flat=yes"
```

#### [Service](http://docs.ansible.com/ansible/service_module.html)

```bash
ansible webservers -m service -a "name=httpd state=started"
ansible webservers -m service -a "name=httpd state=stopped"
ansible webservers -m service -a "name=httpd state=restarted"
ansible webservers -m service -a "name=httpd enabled=yes"
```

#### [APT](http://docs.ansible.com/ansible/apt_module.html)

```bash
# update repositories cache and install package
ansible local -m apt -m "name=foo update_cache=yes"
# install latest version of package
ansible local -m apt -m "name=foo state=latest"
# install version '1.00' of package "foo"
ansible local -m apt -m "name=foo=1.00 state=present"
# remove package
ansible local -m apt -m "name=foo state=absent"
# equivalent of "apt-get update"
ansible local -m apt -m "update_cache=yes"
# install local deb file
ansible local -m apt -m "deb=/tmp/mypackage.deb"
```

#### [APT repository](http://docs.ansible.com/ansible/apt_repository_module.html)

```bash
ansible local -m apt_repository -m "repo='deb http://archive.canonical.com/ubuntu hardy partner' state=present"
ansible local -m apt_repository -m "repo='deb http://archive.canonical.com/ubuntu hardy partner' state=absent"
ansible local -m apt_repository -m "repo='ppa:nginx/stable'"
```

#### [Shell](http://docs.ansible.com/ansible/shell_module.html)

```bash
# execute the command in remote shell
ansible webserver -m shell -a "somescript.sh >> somelog.txt"
# change the working directory to somedir/ before executing the command.
ansible webserver -m shell -a "somescript.sh >> somelog.txt chdir=somedir/"
```
#### [INI](http://docs.ansible.com/ansible/ini_file_module.html)

```bash
ansible webserver -m ini_file -a "dest=/etc/conf section=drinks option=fav value=lemonade mode=0600 backup=yes"
```

#### [Sysctl](http://docs.ansible.com/ansible/sysctl_module.html)

```bash
# set vm.swappiness to 5 in /etc/sysctl.conf
ansible webserver -m sysctl 'name=vm.swappiness value=5 state=present'
# remove kernel.panic entry from /etc/sysctl.conf
ansible webserver -m sysctl 'name=kernel.panic state=absent sysctl_file=/etc/sysctl.conf'
# set kernel.panic to 3 in /tmp/test_sysctl.conf
ansible webserver -m sysctl 'name=kernel.panic value=3 sysctl_file=/tmp/test_sysctl.conf reload=no'
# set ip forwarding on in /proc and do not reload the sysctl file
ansible webserver -m sysctl 'name="net.ipv4.ip_forward" value=1 sysctl_set=yes'
# set ip forwarding on in /proc and in the sysctl file and reload if necessary
ansible webserver -m sysctl 'name="net.ipv4.ip_forward" value=1 sysctl_set=yes state=present reload=yes'
```

#### [Script](http://docs.ansible.com/ansible/script_module.html) - Runs a local script on a remote node after transferring it

```bash
ansible webserver -m script -a "/some/local/script.sh --some-arguments 1234"
```

#### [Git](http://docs.ansible.com/ansible/git_module.html)
```bash
ansible webservers -m git -a "repo=git://foo.example.org/repo.git dest=/srv/myapp version=HEAD"
```

#### [Assemble](http://docs.ansible.com/ansible/assemble_module.html) - Assembles a configuration file from fragments

```bash
ansible webserver -m assemble -a "src=/etc/someapp/fragments dest=/etc/someapp/someapp.conf"
# when a delimiter is specified, it will be inserted in between each fragment
ansible webserver -m assemble -a "src=/etc/someapp/fragments dest=/etc/someapp/someapp.conf delimiter='### START FRAGMENT ###'"
```

#### [LineInFile](http://docs.ansible.com/ansible/lineinfile_module.html)

```bash
ansible webserver -m lineinfile -a 'dest=/etc/sudoers state=absent regexp="^%wheel"'
ansible webserver -m lineinfile -a 'dest=/etc/httpd/conf/httpd.conf regexp="^Listen " insertafter="^#Listen " line="Listen 8080"'
ansible webserver -m lineinfile -a 'dest=/etc/services regexp="^# port for http" insertbefore="^www.*80/tcp" line="# port for http by default"'
ansible webserver -m lineinfile -a 'dest=/tmp/testfile line="192.168.1.99 foo.lab.net foo"'
```

### [Playbooks](http://docs.ansible.com/ansible/playbooks.html)

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
