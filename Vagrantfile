# -*- mode: ruby -*-
# vi: set ft=ruby :

mysql_root_password = "root"

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/bionic64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "./html", "/var/www/html"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
  config.vm.provision "shell", inline: "sudo apt update"

  # Install base
  config.vm.provision "shell", inline: "sudo apt install -qq curl build-essential git-core software-properties-common wget zip -y"

  # Install apache2
  config.vm.provision "shell", inline: "sudo apt install -qq apache2 -y"

  # enable mod_rewrite
  config.vm.provision "shell", inline: "sudo a2enmod rewrite"

  # Enable .htaccess override
  config.vm.provision "file", source: "./vagrant.conf", destination: "$HOME/"
   config.vm.provision "shell", inline: "sudo cp vagrant.conf /etc/apache2/conf-available"
  config.vm.provision "shell", inline: "sudo a2enconf vagrant"

  # Restart Apache
  config.vm.provision "shell", inline: "sudo systemctl restart apache2.service"

  # Install PHP
  config.vm.provision "shell", inline: "sudo apt install -qq libapache2-mod-php7.2 php7.2 php7.2-xml php7.2-gd php7.2-opcache php7.2-mbstring php7.2-common php7.2-mysql php-tokenizer php7.2-cli php7.2-curl php-json php7.2-bcmath -y"

  # enable mcrypt
  config.vm.provision "shell", inline: "sudo apt install  -qq php-dev libmcrypt-dev php-pear -y"
  config.vm.provision "shell", inline: "printf '\n' | sudo pecl install mcrypt-1.0.2"
  config.vm.provision "shell", inline: "sudo printf '%s\n%s\n%s\n' '; configuration for php mcrypt module' '; priority=20' 'extension=mcrypt.so' > /etc/php/7.2/mods-available/mcrypt.ini"
  config.vm.provision "shell", inline: "sudo phpenmod mcrypt"
  config.vm.provision "shell", inline: "sudo systemctl restart apache2.service"

  # Install Composer and place in /usr/local/bin
  config.vm.provision "shell", inline: <<-SHELL
    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php -r "if (hash_file('sha384', 'composer-setup.php') === '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
    php composer-setup.php
    php -r "unlink('composer-setup.php');"
    sudo mv composer.phar /usr/local/bin/composer
  SHELL

  # Set MySQL root password
  config.vm.provision "shell", inline: "sudo debconf-set-selections <<< \"mysql-server mysql-server/root_password password #{mysql_root_password}\""
  config.vm.provision "shell", inline: "sudo debconf-set-selections <<< \"mysql-server mysql-server/root_password_again password #{mysql_root_password}\""
  # Install MySQL 5.7
  config.vm.provision "shell", inline: "sudo apt-get install -qq mysql-server"

  # Adding grant privileges to mysql root user from everywhere
  # http://stackoverflow.com/questions/7528967/how-to-grant-mysql-privileges-in-a-bash-script
  Q1  = "GRANT ALL ON *.* TO 'root'@'%' IDENTIFIED BY '#{mysql_root_password}' WITH GRANT OPTION;"
  Q2  = "FLUSH PRIVILEGES;"
  SQL = "#{Q1}#{Q2}"

  config.vm.provision "shell", inline: "mysql -uroot -p#{mysql_root_password} -e \"#{SQL}\""

end
