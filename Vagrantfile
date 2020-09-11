# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vagrant.plugins = { "vagrant-hosts" => {"version" => "2.9.0"}}

  (1..2).each do |i|
    config.vm.define "host-#{i}" do |host|
      host.vm.box = "centos/7"
      host.vm.box_version = "2004.01"
      
      host.vm.network "private_network", type: "dhcp"

      # Add an additional disk
      host.vm.provider :libvirt do |libvirt|
        libvirt.storage :file, :size => '40G'
      end
    end
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "swarm.yml"
  end
end
