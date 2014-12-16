# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "debian7.1"

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  config.vm.define 'www.rubedo.local' do |front1|
    front1.vm.hostname = 'www.rubedo.local'
    front1.vm.network :private_network, ip: '192.168.56.220'
    front1.vm.synced_folder "src", "/var/www/rubedo", mount_options: ["dmode=775,fmode=775"]
    front1.vm.synced_folder "src/log", "/var/www/rubedo/log", owner: "www-data", group: "www-data", mount_options: ["dmode=775,fmode=664"]
    front1.vm.synced_folder "src/config/", "/var/www/rubedo/config", owner: "www-data", group: "www-data", mount_options: ["dmode=775,fmode=664"]
    front1.vm.provision :shell do |shell|
      shell.inline = '
        wget -qO - https://packages.elasticsearch.org/GPG-KEY-elasticsearch | apt-key add -
        echo "deb http://packages.elasticsearch.org/elasticsearch/1.4/debian stable main" > /etc/apt/sources.list.d/elasticsearch.list
        aptitude update
        aptitude install -y openjdk-7-jre curl curl-devel gcc make mongodb elasticsearch \
            libapache2-mod-php5 php5 php5-cli php5-apc php5-common php5-curl php5-intl php5-gd \
            php-ldap php-odbc php-xml php-xmlrpc php-mbstring php-snmp php-soap php-devel \
            php5-xdebug php-pear php5-dev 
        printf "\n" | pecl install mongo
        echo "extension=mongo.so" > /etc/php5/conf.d/20-mongo.ini
        cp /vagrant/conf/system/sudo /etc/sudoers.d/rubedo
        ln -s /vagrant/conf/apache/rubedo.conf /etc/apache2/sites-enabled/rubedo.conf
        a2dissite default
        a2enmod rewrite headers expires deflate
        /etc/init.d/apache2 restart
        cd /usr/share/elasticsearch
        bin/plugin -install elasticsearch/elasticsearch-mapper-attachments/2.4.1
        bin/plugin -install elasticsearch/elasticsearch-analysis-icu/2.4.1
        update-rc.d elasticsearch defaults 95 10
        curl -sS https://getcomposer.org/installer | php
        mv composer.phar /usr/local/bin/composer
        cd /var/www/rubedo
        bash rubedo.sh
      '
    end
  end
end

