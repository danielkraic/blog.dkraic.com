+++
date = "2015-10-26T19:02:41+01:00"
title = "Vagrant"
tags = [ "dev", "vagrant", "devops" ]
+++

## Vagrant

### Search boxes

[atlas.hashicorp.com/boxes/search](https://atlas.hashicorp.com/boxes/search)

### Up and running

```bash
mkdir my-project && cd my-project
vagrant init hashicorp/precise32
vagrant up
vagrant ssh
vagrant destroy
```

### Commands

#### Init

```bash
# initialize Vagrant environment
vagrant init [box-name] [box-url]
```

#### Up

```bash
# create and configure guest machines
vagrant up
# specify provider (default is virtualbox)
vagrant up --provider virtualbox
# force provisioners to run
vagrant up --provision
# only run given provisioners
vagrant up --provision-with shell,chef
```

#### SSH

```bash
# SSH into running machine
vagrant ssh
```

#### SSH config

```bash
# output valid ssh configuration to SSH into machine
vagrant ssh-config
```

#### Status

```bash
# print machine state
vagrant status
```

#### Global status

```bash
# state of all active Vagrant environments
vagrant global-status
```

#### Halt

```bash
# shut down running machine
vagrant halt
```

#### Reload

```bash
# equivalent of running halt followed by up
vagrant reload
# force provisioners to re-run, provisioners will not run again by default
vagrant reload --provision
```

#### Resume

```bash
# resume machine that was previously suspended
vagrant resume
```

#### Suspend

```bash
# suspends machine (to resume it later)
vagrant suspend
```

#### Destroy

```bash
# stop machine and destroy all its resources
vagrant -f destroy
```


#### Version

```bash
# output vagrant version
vagrant version
```

#### Provision

```bash
# run any configured provisioners
vagrant provision
```

#### Package

```bash
# packages currently running VirtualBox environment into a re-usable box
vagrant package
```

#### Box

```bash
vagrant box list
vagrant box add hashicorp/precise32
vagrant box remove hashicorp/precise32
```

#### Plugin

```bash
vagrant plugin install <name>
vagrant plugin list
vagrant plugin uninstall <name>
vagrant plugin update <name>
```

### Example 

```ruby
# vi: set ft=ruby :

Vagrant.configure(2) do |config|

  # rsyslog server with RELP enabled
  config.vm.define :server do |server|
    server.vm.box = "ubuntu/trusty64"

    server.vm.network "forwarded_port", guest: 514, host: 10514
    server.vm.network "forwarded_port", guest: 20514, host: 20514
    server.vm.network "forwarded_port", guest: 20515, host: 20515

    server.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--usb", "off"]
      vb.customize ["modifyvm", :id, "--usbehci", "off"]
    end

    server.vm.provision :ansible do |ansible|
      ansible.playbook = "ansible/setup-server.yml"
      ansible.host_key_checking = false
      ansible.verbose = "v"
    end
  end

  # rsyslog client with RELP enabled
  config.vm.define :client do |client|
    client.vm.box = "ubuntu/trusty64"

    client.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--usb", "off"]
      vb.customize ["modifyvm", :id, "--usbehci", "off"]
    end

    client.vm.provision :ansible do |ansible|
      ansible.playbook = "ansible/setup-client.yml"
      ansible.host_key_checking = false
      ansible.verbose = "v"
    end
  end
e
```
