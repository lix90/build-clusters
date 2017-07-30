# -*- mode: ruby -*-
# # vi: set ft=ruby :

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
  "spark-master" => { :ip => "192.168.100.50", :cpus => 1, :mem => 1024 },
  "spark-slave1" => { :ip => "192.168.100.51", :cpus => 1, :mem => 1024 },
  "spark-slave2" => { :ip => "192.168.100.52", :cpus => 1, :mem => 1024 },
  # "spark-slave3" => { :ip => "192.168.100.53", :cpus => 1, :mem => 1024 },
}

Vagrant.configure(2) do |config|

  ANSIBLE_RAW_SSH_ARGS = []
  VAGRANT_VM_PROVIDER = "virtualbox"

  cluster.each_with_index do |(hostname, info), index|
    config.vm.define hostname do |cfg|

      cfg.vm.provider :virtualbox do |vb, override|
        override.vm.box = "centos/7"
        override.vm.network :private_network, ip: "#{info[:ip]}"
        if hostname == "spark-master"
          override.vm.network :forwarded_port, guest: 8080, host: 8080
          override.vm.network :forwarded_port, guest: 7077, host: 7077
        end
        override.vm.hostname = hostname

        vb.name = hostname
        vb.customize ["modifyvm", :id,
                      "--memory", info[:mem],
                      "--cpus", info[:cpus],
                      "--hwvirtex", "on"]
      end

      # provision each nodes with ansible at last
      # if index == cluster.length - 1
      #   cfg.vm.provision :ansible do |ansible|
      #     # ansible.verbose = "vvvv"
      #     ansible.inventory_path = "./ansible/inventory"
      #     ansible.playbook = "./ansible/spark-cluster.yml"
      #     ansible.limit = 'all'
      #     ansible.sudo = true
      #     ansible.extra_vars = { ansible_ssh_user: 'vagrant' }
      #   end # end provision
      # end #end if

    end # end config

  end #end cluster

end #end vagrant
