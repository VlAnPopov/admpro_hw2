# -*- mode: ruby -*-
# vim: set ft=ruby :

ENV['VAGRANT_SERVER_URL'] = 'https://vagrant.elab.pro'

MACHINES = {
  :nginx => {
        :box_name => "ubuntu/22.04",
        :vm_name => "nginx",
        :net => [
           ["192.168.56.170","","",""],
        ],
        :ansible => "nginx.yml"
  }
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

    config.vm.define boxname do |box|
   
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxconfig[:vm_name]
      
      box.vm.provider "virtualbox" do |v|
        v.memory = 768
        v.cpus = 1
       end

      #boxconfig[:net].each do |ipconf|
      #  box.vm.network("private_network", ip: ipconf[0], adapter: ipconf[1], netmask: ipconf[2], name: ipconf[3])
      #end
      #Переписано, чтобы создавалась не internal, а host-only сеть
      
	    box.vm.network "private_network", ip: boxconfig[:net][0][0]

      if boxconfig.key?(:public)
        box.vm.network "public_network", boxconfig[:public]
      end


      box.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
        cp ~vagrant/.ssh/auth* ~root/.ssh
        sudo sed -i 's/\#PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
        systemctl restart sshd
      SHELL
      box.vm.provision "ansible" do |ansible|
        ansible.playbook = boxconfig[:ansible]
      end
    end
  end
end