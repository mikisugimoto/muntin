# -*- mode: ruby -*-
# vi: set ft=ruby :

# Require YAML module
require 'yaml'
 
# Read YAML file with box details
inventory = YAML.load_file('inventory.yml')

Vagrant.configure("2") do |config|
    inventory['all']['children']['winvm']['hosts'].each do |host,details|
        config.vm.define host do |hst|
            hst.vm.box = details['vagrant_box']
            hst.vm.hostname = host
            hst.vm.boot_timeout = 600 
            hst.vm.graceful_halt_timeout = 600 
            hst.vm.guest = :windows 
            hst.vm.communicator = "winrm"
            hst.vm.network :private_network, ip: details['ansible_host']
            inventory['all']['vars']['vagrant_ports'].each do |protocol,details|
                hst.vm.network :forwarded_port, guest: details['guest'], host: details['host'], id: protocol
            end

            hst.vm.provider :virtualbox do |v|
                v.name = host
                v.memory = 8192
                v.cpus = 4
            end
        
            config.vm.provision "ansible" do |ansible|
                ansible.playbook = "winvm_setup.yml"
                ansible.limit = "all"
                ansible.inventory_path = "inventory.yml"
                ansible.verbose = "-vv"
            end
        end
    end
end
