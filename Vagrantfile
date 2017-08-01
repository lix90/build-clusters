# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrant.configure(2) do |config|

#   ANSIBLE_RAW_SSH_ARGS = []
#   VAGRANT_VM_PROVIDER = "virtualbox"
#   machine_box = "centos/7"

#   config.vm.define "spark-master" do |machine|
#     machine.vm.box = machine_box
#     machine.vm.hostname = "spark-master"
#     machine.vm.network "private_network", ip: "192.168.100.50"
#     machine.vm.provider "virtualbox" do |node|
#       node.name = "spark-master"
#       node.memory = 1024
#       node.cpus = 1
#     end
#   end

#   config.vm.define "spark-slave1" do |machine|
#     machine.vm.box = machine_box
#     machine.vm.hostname = "spark-slave1"
#     machine.vm.network "private_network", ip: "192.168.100.51"
#     machine.vm.provider "virtualbox" do |node|
#       node.name = "spark-slave1"
#       node.memory = 1024
#       node.cpus = 1
#     end
#   end

#   config.vm.define "spark-slave2" do |machine|
#     machine.vm.box = machine_box
#     machine.vm.hostname = "spark-slave2"
#     machine.vm.network "private_network", ip: "192.168.100.52"
#     machine.vm.provider "virtualbox" do |node|
#       node.name = "spark-slave2"
#       node.memory = 1024
#       node.cpus = 1
#     end
#   end

#   config.vm.provision :ansible do |ansible|
#     ansible.verbose = "vvv"
#     ansible.inventory_path = "./ansible/inventory"
#     ansible.playbook = "./ansible/spark-cluster.yml"
#     ansible.limit = 'all'
#     ansible.sudo = true
#     # ansible.extra_vars = { ansible_ssh_user: 'vagrant' }
#   end # end provision

# end



cluster = {
  "spark-master" => { :ip => "192.168.100.50", :cpus => 1, :mem => 512 },
  "spark-slave1" => { :ip => "192.168.100.51", :cpus => 1, :mem => 512 },
  "spark-slave2" => { :ip => "192.168.100.52", :cpus => 1, :mem => 512 },
  # "spark-slave3" => { :ip => "192.168.100.53", :cpus => 1, :mem => 512 },
}

Vagrant.configure(2) do |config|

  ANSIBLE_RAW_SSH_ARGS = []
  VAGRANT_VM_PROVIDER = "virtualbox"
  config.ssh.insert_key = false
  # config.ssh.private_key_path = "~/.ssh/id_rsa"
  # config.ssh.username = "vagrant" 
  # config.ssh.password = "vagrant"

  # Manage /etc/hosts on host and VMs
  config.hostmanager.enabled = false
  config.hostmanager.manage_host = true
  config.hostmanager.include_offline = true
  config.hostmanager.ignore_private_ip = false
  
  cluster.each_with_index do |(hostname, info), index|
    config.vm.define hostname do |cfg|
      cfg.vm.provider :virtualbox do |vb, override|

        override.vm.box = "centos/7"
        override.vm.network :private_network, ip: "#{info[:ip]}" 
        override.vm.hostname = hostname

        vb.name = hostname
        vb.customize ["modifyvm", :id,
                      "--memory", info[:mem],
                      "--cpus", info[:cpus],
                      "--hwvirtex", "on"]
      end

      config.vm.provision :hostmanager
      
      if index == cluster.size - 1
        cfg.vm.provision :ansible do |ansible|
          ansible.verbose = "v"
          ansible.inventory_path = "inventory"
          ansible.playbook = "playbook.yml"
          ansible.limit = 'all'
          ansible.groups = {
            "master" => ["spark-master"],
            "slaves" => ["spark-slave[1:2]"]
          }
          # ansible.sudo = true
          # ansible.extra_vars = { ansible_ssh_user: 'vagrant' }
        end # end provision
        
      end #end if

      
    end # end config

  end #end cluster

end #end vagrant
