# -*- mode: ruby -*-
# vi: set ft=ruby :

# This is intended to be a drop-in Vagrantfile, which reads VM configurations
# from two yaml files (boxes.yml and nodes.yml) in the root directory.
# See the README for more thorough documentation.

# We're going to read node (vagrant vm instance) and box info from yaml files,
# so we gots to know how to yaml
require 'yaml'

# Read box and node configs
root_dir = File.dirname(__FILE__)
boxes = YAML.load_file("#{root_dir}/boxes.yml")
nodes = YAML.load_file("#{root_dir}/nodes.yml")

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Define vagrant VMs for each node defined in nodes.yml
  nodes.each do |node_name, node_details|
    box_name = node_details['box']
    box = boxes[box_name]

    config.vm.define node_name do |node|
      node.vm.box = box_name
      node.vm.box_url = box['url']
      node.vm.hostname = node_details['hostname']
      node.vm.network "private_network", ip: node_details['ip']
    
      node_details['synced_folders'].each do |synced_folder|
        node.vm.synced_folder synced_folder['source'], synced_folder['dest']
      end

      node.vm.provider :virtualbox do |vb|
        vb.name = node_name
        vb.customize [ "modifyvm", :id, "--memory", node_details['memory'] ]
        vb.customize [ "modifyvm", :id, "--cpus", node_details['cpus'] ]
      end
    end
  end

end
