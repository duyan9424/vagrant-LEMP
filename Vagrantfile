$script = <<SCRIPT
#!/usr/bin/env bash

echo "Provisioning virtual machine..."

echo "Find Out Linux Distribution Name and Release Version"
cat /etc/lsb-release

echo "Updating apt-get..."
sudo apt-get update > /dev/null

# Nginx
echo "Installing Nginx..."
sudo apt-get install -y nginx > /dev/null

# PHP
echo "Updating PHP repository..."
sudo apt-get install -y python-software-properties build-essential > /dev/null
sudo add-apt-repository -y ppa:ondrej/php > /dev/null

echo "Updating apt-get once more..."
sudo apt-get update > /dev/null

echo "Installing PHP..."
sudo apt-get install -y php7.0 php7.0-fpm > /dev/null

echo "Installing PHP extensions..."
sudo apt-get install -y curl php7.0-mysql php7.0-simplexml php7.0-curl php7.0-zip > /dev/null

# MySQL
echo "Preparing MySQL..."
sudo apt-get install -y debconf-utils > /dev/null
debconf-set-selections <<< "mysql-server mysql-server/root_password password root"
debconf-set-selections <<< "mysql-server mysql-server/root_password_again password root"

echo "Installing MySQL..."
sudo apt-get install -y mysql-server > /dev/null

# Nginx Config
echo "Configuring Nginx..."

sudo rm /etc/nginx/sites-available/default
sudo touch /etc/nginx/sites-available/default

cat << 'EOF' > /tmp/default
server {
  listen 80;
  root /var/www/project/public/;
  index index.php index.html index.htm;
  index index.php index.html;

  # Important for VirtualBox
  sendfile off;

  location / {
	try_files $uri $uri/ =404;
  }

  location ~* \.php {
	include fastcgi_params;

	fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;

	fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
	fastcgi_cache off;
	fastcgi_index index.php;
  }
}
EOF
# nginx_vhost
sudo mv /tmp/default /etc/nginx/sites-available/default

echo "Preparing Nginx web server relation with PHP FastCGI process manager..."
# To test Nginx web server relation with PHP FastCGI process manager
mkdir '/var/www/project/public'
sudo su -c 'echo "<?php phpinfo(); ?>" |tee /var/www/project/public/index.php'
echo "Your access: http://IP_or_domain/index.php"

# Restarting Nginx for config to take effect
echo "Restarting Nginx..."
sudo service nginx restart > /dev/null
sudo service php7.0-fpm restart > /dev/null

echo "-- Install Composer --"
curl -s https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
sudo chmod +x /usr/local/bin/composer

SCRIPT

# Fix the warning: dpkg-reconfigure: unable to re-open stdin: No file or directory
# export DEBIAN_FRONTEND=noninteractive

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  
  config.vm.box = "ubuntu/xenial64"
  config.vm.network "private_network", ip: "192.168.33.100"

  # config.vm.network :forwarded_port, guest: 80, host: 8080, auto_correct: true

  config.vm.synced_folder "./", "/vagrant", disabled: true
  config.vm.synced_folder "./project", "/var/www/project", create: true, group: "www-data", owner: "www-data"

  config.vm.provider :virtualbox do |vb|
    vb.name = "Vagrant LEMP Stack"
    vb.customize ["modifyvm", :id, "--memory", "1024"]
  end
  
  config.vm.provision "shell", inline: $script
end
