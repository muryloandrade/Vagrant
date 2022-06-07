$script_mysql = <<-SCRIPT
  apt-get update && \
  apt-get install -y mysql-server-5.7 && \
  mysql -e "create user 'phpuser'@'%' identified by 'pass';"
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 512
    vb.cpus = 1
    vb.name = "ubuntu_bionic_php"
  config.vm.define "mysqldb" do |mysql|
  
  mysql.vm.network "public_network", ip:"172.20.0.57", bridge: "wlp0s20f3"
  
  mysql.vm.provision "shell", 
  inline:"cat /configs/idbio >> .ssh/authorized_keys"
  mysql.vm.provision "shell", 
  inline: $script_mysql
  mysql.vm.provision "shell", 
  inline:"cat /configs/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf"
  mysql.vm.provision "shell", 
  inline:"service mysql restart"
  
  mysql.vm.synced_folder "./configs", "/configs"
  mysql.vm.synced_folder ".", "/vagrant", disabled: true
end


config.vm.define "phpweb" do |phpweb|

  phpweb.vm.network "forwarded_port", guest:8097 , host:8097
  phpweb.vm.network "public_network", ip:"172.20.0.58", bridge:"wlp0s20f3"
  
  phpweb.vm.provision "shell",
    inline:"apt-get update && apt-get install -y puppet"
  
  phpweb.vm.provision "puppet" do |puppet|
    puppet.manifests_path = "./configs/manifest"
    puppet.manifest_file = "phpweb.pp"

  end
end
end

Vagrant.configure("2") do |config|

config.vm.define "mysqlserver" do |mysqlserver|
  mysqlserver.vm.network "public_network", ip:"172.20.0.56", bridge:"wlp0s20f3"
  mysqlserver.vm.provision "shell", 
  inline:"cat /vagrant/configs/idbio >> .ssh/authorized_keys"

end



config.vm.define "ansible" do |ansible|
  
ansible.vm.provision "shell",
  inline: "cp vagrant/idbio /home/vagrant && \
           chmod 600 /home/vagrant/idbio && \
           chown vagrant:vagrant /home/vagrant/idbio"

  ansible.vm.provision "shell", 
  inline:"apt-get update && \
        apt-get install -y software-properties-common && \
        apt-add-repository --yes --update ppa:ansible/ansible && \
        apt-get install -y ansible"
  
  
  ansible.vm.provision "shell",
  inline: "ansible-playbook -i /vagrant/configs/ansible/hosts /vagrant/configs/ansible/playbook.yml"
end

config.vm.define "memcached" do |memcached|
  memcached.vm.box = "centos/7"
  vb.name = "centos"
  vb.memory = 512
  vb.cpus = 1



    end
  end 
end