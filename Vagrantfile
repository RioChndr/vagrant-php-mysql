Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/lunar64"

  config.vm.provision "shell", inline: <<-SHELL
    # update package lists
    sudo apt-get update
    
    # install php and extensions
    sudo apt-get install -y php php-cli php-common php-curl php-mbstring php-mysql php-xml php-dom php-mysqli php-json php-exif php-fileinfo php-hash php-imagick php-mbstring php-openssl php-pcre php-xml php-zip php-ftp
    sudo apt-get install -y composer
    
    # install mysql server
    sudo apt-get install -y mariadb-server mariadb-client 
    
    # allow access mariadb from outside
    sudo echo -e "[mysqld]\nbind-address = 0.0.0.0" >> /etc/mysql/my.cnf

    # set root password
    sudo /usr/bin/mysqladmin -u root password 'password'

    # allow remote access (required to access from our private network host. Note that this is completely insecure if used in any other way)
    mysql -u root -ppassword -e "GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION; FLUSH PRIVILEGES;"

    # drop the anonymous users
    mysql -u root -ppassword -e "DROP USER ''@'localhost';"
    mysql -u root -ppassword -e "DROP USER ''@'$(hostname)';"

    # drop the demo database
    mysql -u root -ppassword -e "DROP DATABASE test;"

    # restart
    sudo systemctl restart mysql
    sudo systemctl restart mariadb
  SHELL

  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 3306, host: 3306

  config.vm.synced_folder "html/", "/var/www/html", :mount_options => ["dmode=777", "fmode=666"]
end

