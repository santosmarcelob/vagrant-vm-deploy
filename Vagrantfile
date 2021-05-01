# Script para instalação das aplicações e configurações
$script = <<-'SCRIPT'
    sudo yum -y install vim
    sudo yum -y install wget
    sudo wget --no-check-certificate -c --header 'Cookie: oraclelicense=accept-securebackup-cookie' https://download.oracle.com/otn-pub/java/jdk/16.0.1+9/7147401fd7354114ac51ef3e1328291f/jdk-16.0.1_linux-x64_bin.rpm
    sudo rpm -ivh jdk-16.0.1_linux-x64_bin.rpm
    echo "export JAVA_HOME=\"/usr/java/jdk-16.0.1/bin/java\"" | sudo tee -a /etc/profile.d/myvar.sh
    echo "export NUMERO_DA_SORTE=777" | sudo tee -a /etc/profile.d/myvar.sh
    sudo shutdown -r now
  SCRIPT

# Define numero de VMs
vms = 3 
 
Vagrant.configure("2") do |config|
  
  config.vm.box = "centos/8"

  (1..vms).each do |i| # Cria "N" VMs (Numero de VMs definido pela variável "vms")
    config.vm.define "host#{i}" do |host|
      host.vm.hostname = "host#{i}" # incrementa hostname automaticamente para cada VM
      host.vm.network "private_network", ip: "172.16.5.#{i+1}" # incrementa IP automaticamente para cada VM
      host.vm.provision "shell", inline: $script
    end
  end
end
