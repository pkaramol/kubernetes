# -*- mode: ruby -*-
# vi: set ft=ruby :
# N corresponds to the number of secondary MongoDB nodes

MASTERS = 1
NODES = 2


ansible_groups = {
  "masters" => [
    "kubernetes.master.[1:#{MASTERS}]"
  ],
  "nodes" => [
    "kubernetes.node.[1:#{NODES}]"
  ]
}

Vagrant.configure("2") do |config|

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
  config.vm.box = "ubuntu/xenial64"
  config.vm.box_check_update = true




  (1..MASTERS).each do |master_no|
    config.vm.define "kubernetes-master-#{master_no}" do |master|
      master.vm.hostname = "master-#{master_no}"
      master.vm.network "private_network", ip: "192.168.33.#{master_no}"
      master.vm.provider "virtualbox" do |vb|
        vb.cpus = "2"
        vb.memory = "4096"
      end

      master.vm.provision "hostmanager"
      master.vm.provision "shell",
        inline: "test -e /usr/bin/python || (apt-get -qqy update && apt-get install -qqy python-minimal)"
      master.vm.provision "ansible" do |ansible|
          ansible.playbook = "ansible/playbooks/kubernetes.yml"
          ansible.groups = ansible_groups
          ansible.config_file = "ansible/ansible.cfg"
          # ansible.verbose = "True"
      end
    end
  end

  (1..NODES).each do |node_no|
    config.vm.define "kubernetes-node-#{node_no}" do |node|
      node.vm.hostname = "node-#{node_no}"
      node.vm.network "private_network", ip: "192.168.33.#{node_no}"
      node.vm.provider "virtualbox" do |vb|
        vb.cpus = "2"
        vb.memory = "4096"
      end
    node.vm.provision "hostmanager"
    node.vm.provision "shell",
      inline: "test -e /usr/bin/python || (apt-get -qqy update && apt-get install -qqy python-minimal)"
    node.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/playbooks/kubernetes.yml"
      ansible.groups = ansible_groups
      ansible.config_file = "ansible/ansible.cfg"
      # ansible.verbose = "True"
    end
  end
  end

end