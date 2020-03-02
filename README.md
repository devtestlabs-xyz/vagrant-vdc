# Vagrant VNet
This solution allows users to quickly and easily employ [Hashicorp Vagrant](https://www.vagrantup.com/) to provision a virtual network comprised of _n_ virtual machines for development, testing, research, and learning. This environment was created and tested using Vagrant Virtualbox provider on Windows, Linux, and MacOS hosts.

This project is inspired by Scott Lowe's article, ["An Improved Way to use YAML with Vagrant"](https://blog.scottlowe.org/2016/01/14/improved-way-yaml-vagrant/)

## Assets

* **machines.yml**: This YAML file contains a list of VM definitions and associated configuration data. It is referenced by `Vagrantfile` when Vagrant instantiates the VMs. Generally, this is the _only_ file that requires any edits; you'll edit this file to specify the correct Vagrant box installed on your system. THis file contains a large number of virtual machine definitions. DO NOT `vagrant up` as Vagrant will attempt to start all of the machines defined in this file and it will overwhelm your host workstation. Instead, `vagrant up {{ SOME_BOX_NAME }}` to start a single virtual machine or `vagrant up /{{ SOME_REG_EX_BOX_NAME_PATTERN }} /` to start multiple virtual machines.

* **Vagrantfile**: This file is used by Vagrant to spin up the virtual machines. You should be able to use this file unchanged; all the VM configuration options are stored outside this file.

* **vagrant_insecure_rsa**: Publicly known vagrant private RSA key that is used by default in Vagrant. Please note that this is insecure as it is publicly available. This key should only be used for local devtest virtual machines that do not have access to any real enterprise resources.
In otherwords, ensure your Vagrant vNet is HOST_ONLY (PRIVATE). https://github.com/hashicorp/vagrant/tree/master/keys

## Getting Started

### Preflight
These instructions assume you've already installed VirtualBox and Vagrant. Please refer to the documentation for those products for more information on installation or configuration.

#### VirtualBox
https://www.virtualbox.org/wiki/Downloads
 
Windows users will want to install the VirtualBox 6.1.0 platform for Windows hosts
https://download.virtualbox.org/virtualbox/6.0.10/VirtualBox-6.0.10-132072-Win.exe
 
_Note: Do NOT install VirtualBox Extension Pack or Software Developer Kit. Both of these packages are not needed and the Extension Pack requires an Enterprise license._
 
#### Vagrant
https://www.vagrantup.com/
 
Windows users will want to install the Windows 64bit package
https://releases.hashicorp.com/vagrant/2.2.5/vagrant_2.2.5_x86_64.msi

https://www.virtualbox.org/wiki/Documentation

https://www.vagrantup.com/docs/

### Add Vagrant boxes
The `machines.yaml` file references the following four publicly available Vagrant boxes:

* **ryancraig/fedora30-dev-ws**: A Fedora 30 development workstation. See https://app.vagrantup.com/ryancraig/boxes/fedora30-dev-ws for details.

* **ryancraig/fedora31-dev-ws**: A Fedora 31 development workstation. See https://app.vagrantup.com/ryancraig/boxes/fedora31-dev-ws for details.

* **centos/7**: A headless CentOS 7 minimal server. See https://app.vagrantup.com/centos/boxes/7 for details.

* **gusztavvargadr/windows-server 1809.0.1907-standard-core**: A Windows 2019 Server Standard Core with OpenSSH configured and running. See https://app.vagrantup.com/gusztavvargadr/boxes/windows-server/versions/1809.0.1907-standard-core for details.

* **gusztavvargadr/windows-server 1809.0.1907-standard**: A Windows 2019 Server Standard GUI Experience with OpenSSH configured and running. See https://app.vagrantup.com/gusztavvargadr/boxes/windows-server/versions/1809.0.1907-standard for details.

You will need to download each of these boxes before instantiating instances of them using the included `Vagrantfile`.

1. `vagrant box add centos/7`; choose `3` for the VirtualBox flavor

1. `vagrant box add ryancraig/fedora30-dev-ws`

1. `vagrant box add gusztavvargadr/windows-server --box-version 1809.0.1907-standard`; choose `2` for the VirtualBox flavor

1. `vagrant box add gusztavvargadr/windows-server --box-version 1809.0.1907-standard-core`; choose `2` for the VirtualBox flavor

*NOTE: The machine inventory specified in the included `machines.yaml` file can be changed to include or exclude machines. This file should be seen as an example and a nice springboard for your own efforts.*

### Start a subset of virtual machines
There are alot of virtual machines defined in the `machines.yaml` file. If you execute `vagrant up`, Vagrant will attempt to start all of the machines defined in this file and it will overwhelm your host workstation. Instead, you will need to identify a specific subset of virtual machines to start. Vagrant understands regular expressions so you can start multiple machines using `vagrant up /{{ SOME_REG_EX_PATTERN }}/`. For example, let's say you want to start virtual machines named `ansible-dev-ws`, `centos7-host1`, `centos7-host2`, `centos7-host3`, and `ad-090`. You can execute a single command to start all of these virtual machines. The command is `vagrant up /ansible*\|centos7-host[1-3]\|ad-090/` (this assumes that `ansible*` will only match `ansible-dev-ws`). See https://www.vagrantup.com/docs/multi-machine/#controlling-multiple-machines for more information about controlling multiple virtual machines.

## Additional Configuration
If you are using multiple virtual machines, especially Windows virtual machines, you may run out of disk space quickly. You may need to employ an external disk to add disk capacity. In order to make use of this additional disk capacity, you must reconfigure VirtualBox and Vagrant to store virtual machines and other meta data on this external disk capacity rather than within your user's home path (which is probably stored on your limited capacity internal disk).

The way in which you reconfigure Vagrant and VirtualBox a slightly different on each OS so you will need to research how to migrate existing Vagrantboxes within `~/.vagrant.d` to `/external/disk/path/.vagrant.d` and set `VAGRANT_HOME` environment variable.

## External References
* https://www.vagrantup.com/
 
* https://releases.hashicorp.com/vagrant/2.2.5/vagrant_2.2.5_x86_64.msi

* https://www.virtualbox.org/wiki/Documentation

* https://www.vagrantup.com/docs/

* https://www.virtualbox.org/wiki/Downloads

* https://download.virtualbox.org/virtualbox/6.0.10/VirtualBox-6.0.10-132072-Win.exe

* https://www.jeffgeerling.com/blogs/jeff-geerling/running-vagrant-virtualbox

* https://github.com/hashicorp/vagrant/tree/master/keys

* https://blog.scottlowe.org/2016/01/14/improved-way-yaml-vagrant/