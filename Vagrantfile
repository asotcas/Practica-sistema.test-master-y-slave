Vagrant.configure("2") do |config|
 
  config.vm.define "dns_master" do |master|
    master.vm.box = "debian/buster64"
    master.vm.network "private_network", ip: "192.168.57.101"
  end

  
  config.vm.define "dns_slave" do |slave|
    slave.vm.box = "debian/buster64"
    slave.vm.network "private_network", ip: "192.168.57.102"
  end

  config.vm.define "venus" do |venus|
    venus.vm.box = "debian/buster64"
    venus.vm.network "private_network", ip: "192.168.57.103"
  end

  config.vm.define "marte" do |marte|
    marte.vm.box = "windows/servercore"
    marte.vm.network "private_network", ip: "192.168.57.104"
  end
end

