$script_ansible = <<-SCRIPT
apt-get update && \
apt install software-properties-common -y && \ 
apt install ansible -y
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  #Synced Folders
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.synced_folder "./configs", "/configs"

  #Wordpress
  config.vm.define "wordpress" do |wordpress|
      #Configuration
      wordpress.vm.provider "virtualbox" do |vb|
        vb.memory = 1024
        vb.cpus = 2
        vb.name = "wordpress"
      end
      #Private Network manually assigned
      wordpress.vm.network "private_network", ip: "192.168.182.11"    
      #Provisioning
      wordpress.vm.provision "shell", inline: "apt-get update -y && sudo apt install python -y"
      wordpress.vm.provision "shell",
        inline: "cat /configs/wp_key.pub >> .ssh/authorized_keys"
  end

  #Ansible
  config.vm.define "ansible" do |ansible|
      #Configuration
      ansible.vm.provider "virtualbox" do |vb|
        vb.memory = 512
        vb.cpus = 2
        vb.name = "ansible"
      end
      #Public Network manually assigned
      ansible.vm.network "private_network", ip: "192.168.182.12" 
      #Provisioning
      ansible.vm.provision "shell", 
        inline: "cp /configs/wp_key /home/vagrant && \ 
                chmod 600 /home/vagrant/wp_key && \
                chown vagrant:vagrant /home/vagrant/wp_key "
      ansible.vm.provision "shell", inline: $script_ansible
      ansible.vm.provision "shell", inline: "ansible-playbook -i /configs/playbooks/hosts /configs/playbooks/provisioning.yml"
  end

end