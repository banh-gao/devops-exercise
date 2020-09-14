# -*- mode: ruby -*-
# vi: set ft=ruby :
require_relative './vagrant_key_authorization'

Vagrant.configure("2") do |config|
  config.vagrant.plugins = { "vagrant-hostmanager" => {"version" => "1.8.9"}}
  config.ssh.insert_key = false
  
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true

  authorize_key_for_root config, '~/.ssh/id_rsa.pub'

  (1..2).each do |i|
    config.vm.define "node#{i}.test" do |host|
      host.vm.box = "centos/7"
      host.vm.box_version = "2004.01"

      host.vm.hostname = "node#{i}.test"
      host.vm.network "forwarded_port", guest: 2376, host: 2376

      # Add an additional disk
      host.vm.provider :libvirt do |libvirt|
        libvirt.storage :file, :size => '40G'
      end
    end
  end
end
