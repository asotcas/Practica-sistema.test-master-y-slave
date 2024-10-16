
Vagrant.configure("2") do |config|

  config.vm.define "mercurio" do |mercurio|
    mercurio.vm.box = "ubuntu/bionic64"  
    mercurio.vm.network "private_network", ip: "192.168.57.101"
    mercurio.vm.hostname = "mercurio.sistema.test"

    mercurio.vm.provision "shell", inline: <<-SHELL
      echo "Configurando Mercurio..."
      
    SHELL
  end

 
  config.vm.define "venus" do |venus|
    venus.vm.box = "debian/buster64"
    venus.vm.network "private_network", ip: "192.168.57.102"
    venus.vm.hostname = "venus.sistema.test"

    venus.vm.provision "shell", inline: <<-SHELL
      echo "Configurando Venus..."
      
    SHELL
  end

  
  config.vm.define "tierra" do |tierra|
    tierra.vm.box = "debian/buster64"
    tierra.vm.network "private_network", ip: "192.168.57.103"
    tierra.vm.hostname = "tierra.sistema.test"

    tierra.vm.provision "shell", inline: <<-SHELL
      echo "Configurando esta Tierra..."
      
    SHELL
  end


  config.vm.define "marte" do |marte|
    marte.vm.box = "peru/windows-2019"  
    marte.vm.network "private_network", ip: "192.168.57.104"
    marte.vm.hostname = "marte.sistema.test"

    marte.vm.provision "shell", inline: <<-SHELL
      echo "Configurando Marte..."
      
    SHELL
  end
end

