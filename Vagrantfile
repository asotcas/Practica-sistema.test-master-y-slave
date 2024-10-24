# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  

  config.vm.define "dns_master" do |master|
    master.vm.box = "debian/bullseye64" 
    master.vm.hostname = "master.sistema.test"  
    master.vm.network "private_network", ip: "192.168.57.101"  
    master.vm.provider "virtualbox" do |vb|
      vb.memory = "512"  
    end
    master.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y bind9 bind9utils bind9-doc  
    SHELL
  end


  config.vm.define "dns_slave" do |slave|
    slave.vm.box = "debian/bullseye64"
    slave.vm.hostname = "slave.sistema.test"
    slave.vm.network "private_network", ip: "192.168.57.102"
    slave.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
    end
    slave.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y bind9 bind9utils bind9-doc  
    SHELL
  end

end