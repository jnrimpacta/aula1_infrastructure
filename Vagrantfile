$script = <<SCRIPT

echo -e "\n--- Atualizando pacotes ---\n"
sudo apt-get -qq update

#Variáveis para o MySQL - Apenas para máquina de desenvolvimento
#Altere para os dados que achar relevante
DBHOST=localhost
DBNAME=vagrant
DBUSER=user_vagrant
DBPASSWD=vagrant

echo -e "\n--- Instalando e configurando MySQL ---\n"
debconf-set-selections <<< "mysql-server mysql-server/root_password password $DBPASSWD"
debconf-set-selections <<< "mysql-server mysql-server/root_password_again password $DBPASSWD"
sudo apt-get -y install mysql-server

mysql -uroot -p$DBPASSWD -e "CREATE DATABASE $DBNAME"
mysql -uroot -p$DBPASSWD -e "grant all privileges on $DBNAME.* to '$DBUSER'@'%' identified by '$DBPASSWD'"

systemctl enable mysql

sed -i "s/bind-address.*/bind-address = 0.0.0.0/" /etc/mysql/mysql.conf.d/mysqld.cnf
sudo service mysql restart

echo -e "\n--- MYSQL instalado com sucesso!! ---\n"
SCRIPT

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/xenial64"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 1024
    vb.cpus = 1
  end

  config.vm.define "mysqlserver" do |mysqlserver|
    mysqlserver.vm.network "private_network", ip: "10.80.4.10"
    config.vm.network "forwarded_port", guest: 3306, host: 3306

    mysqlserver.vm.provider "virtualbox" do |vb|
      vb.name = "mysqlserver"
    end

    config.vm.provision "shell", inline: $script
  end
  
end