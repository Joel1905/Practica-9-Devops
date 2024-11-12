# Vagrantfile para 2 servidores Apache y 1 servidor Nginx como balanceador de carga
Vagrant.configure("2") do |config|
  
    # Servidor Web 1 (Apache)
    config.vm.define "web1" do |web1|
      web1.vm.box = "ubuntu/jammy64"
      web1.vm.hostname = "web1"
      web1.vm.network "private_network", ip: "192.168.56.10"
      web1.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      web1.vm.synced_folder "./web1", "/var/www/html", create: true
      web1.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install -y apache2
        echo "Servidor Apache - Web1 (192.168.56.10)" > /var/www/html/index.html
        systemctl enable apache2
        systemctl start apache2
      SHELL
    end
  
    # Servidor Web 2 (Apache)
    config.vm.define "web2" do |web2|
      web2.vm.box = "ubuntu/jammy64"
      web2.vm.hostname = "web2"
      web2.vm.network "private_network", ip: "192.168.56.11"
      web2.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      web2.vm.synced_folder "./web2", "/var/www/html", create: true
      web2.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install -y apache2
        echo "Servidor Apache - Web2 (192.168.56.11)" > /var/www/html/index.html
        systemctl enable apache2
        systemctl start apache2
      SHELL
    end
  
    # Servidor Nginx (Load Balancer)
    config.vm.define "loadbalancer" do |loadbalancer|
      loadbalancer.vm.box = "ubuntu/bionic64"
      loadbalancer.vm.hostname = "loadbalancer"
      loadbalancer.vm.network "private_network", ip: "192.168.56.12"
      loadbalancer.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      loadbalancer.vm.provision "shell", inline: <<-SHELL
        apt-get update
        apt-get install -y nginx
        cat > /etc/nginx/sites-available/default <<EOL
  server {
      listen 80;
      location / {
          proxy_pass http://web_backend;
      }
  }
  
  upstream web_backend {
      server 192.168.56.10;
      server 192.168.56.11;
  }
  EOL
        systemctl enable nginx
        systemctl restart nginx
      SHELL
    end
  
  end
  