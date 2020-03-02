# -*- mode: ruby -*-
# vi: set ft=ruby :

# Specify Vagrant version, Vagrant API version, and Vagrant clone location
Vagrant.require_version '>= 1.6.0'
VAGRANTFILE_API_VERSION = '2'

# Require 'yaml' module
require 'yaml'

# Read YAML file with VM details (box, CPU, and RAM)
machines = YAML.load_file(File.join(File.dirname(__FILE__), 'machines.yaml'))

# Create and configure the VMs
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Always use Vagrant's default insecure key
  config.ssh.insert_key = false

  # Iterate through entries in YAML file to create VMs
  machines.each do |machine|
    config.vm.define machine['vagrant_host_name'] do |srv|

      # Don't check for box updates
      srv.vm.box_check_update = false
      srv.vm.hostname = machine['vagrant_host_name']
      srv.vm.box = machine['vagrant_vb_box']

      # Configure default synced folder (disable by default)
      if machine['sync_disabled'] != nil
        srv.vm.synced_folder '.', '/vagrant', disabled: machine['sync_disabled']
      else
        srv.vm.synced_folder '.', '/vagrant', disabled: true
      end #if machine['sync_disabled']

      # Assign additional private network, if specified in machines.yml
      # In order to emulate a truly private network in VirtualBox you must use `virtualbox__intnet`
      if machine['remote_net_ip_addr'] != nil
        srv.vm.network "private_network", ip: machine['remote_net_ip_addr'], forward_mode: "none", virtualbox__intnet: "myprivatevnet"
      end # if machine['private_net_ip_addr']

      # Assign additional local network(actually just another host-only network), if specified in machines.yml
      if machine['local_net_ip_addr'] != nil
        srv.vm.network "private_network", ip: machine['local_net_ip_addr'], forward_mode: "none"
      end # if machine['local_net_ip_addr']

      # Configure CPU & RAM per settings in machines.yml (VirtualBox)
      srv.vm.provider 'virtualbox' do |vb, override|
        vb.memory = machine['vagrant_ram']
        vb.cpus = machine['vagrant_vcpu']
        #override.vm.box = machine['vagrant_vb_box']
        if machine['vagrant_box_version'] != nil
          override.vm.box_version = machine['vagrant_box_version']
        end #if machine['vagrant_box_version']
        vb.gui = machine['vagrant_gui_mode']
      end # srv.vm.provider virtualbox

      # Provision key pairs according to VM name
      # If 'client-node' VM, copy in all keys
      if machine['vagrant_role'] == 'client-node'
        #srv.vm.provision 'file', source: 'client-ssh-config',
        #  destination: '/home/vagrant/.ssh/config'
        srv.vm.provision 'file', source: 'vagrant_insecure_rsa',
           destination: '/home/vagrant/.ssh/vagrant_insecure_rsa'
        srv.vm.provision "shell",
           inline: "chmod 400 /home/vagrant/.ssh/vagrant_insecure_rsa"
      end # if 'client-node'

      # If 'bastion-node' VM, echo bastion_rsa.pub to authorized_keys
      if machine['vagrant_role'] == 'bastion-node'
        # PLACEHOLDER
      end # if 'bastion-node'

      # If 'remote-node' VM, echo remote_rsa.pub to authorized_keys
      if machine['vagrant_role'] == 'remote-node'
        # PLACEHOLDER
      end # if 'remote-node'
    end # config.vm.define
  end # machines.each
end # Vagrant.configure
