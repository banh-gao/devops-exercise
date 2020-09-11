# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vagrant.plugins = { "vagrant-hosts" => {"version" => "2.9.0"}}

  (1..2).each do |i|
    config.vm.define "node#{i}" do |host|
      host.vm.box = "centos/7"
      host.vm.box_version = "2004.01"

      host.vm.hostname = "node#{i}"
      host.vm.network "private_network", ip: "10.0.0.#{i}0"

      # Add an additional disk
      host.vm.provider :libvirt do |libvirt|
        libvirt.storage :file, :size => '40G'
      end
    end
  end

  config.vm.provision :hosts, :sync_hosts => true

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "swarm.yml"
    ansible.galaxy_role_file = "requirements.yml"
    ansible.groups = {
      "docker_swarm_manager" => ["node1"],
      "docker_swarm_worker" => ["node2"],
  }
  end
end
