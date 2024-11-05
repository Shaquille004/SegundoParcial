# Vagrantfile para 2 servidores Apache y 1 NGINX con balanceo de carga

Vagrant.configure("2") do |config|
    # Configuración de la primera máquina (Servidor Apache 1)
config.vm.define "apache1" do |apache1|  # Corregido aquí
    apache1.vm.box = "ubuntu/jammy64"
    apache1.vm.network "private_network", ip: "192.168.56.101"
    apache1.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
    end
    # Directorio compartido en la máquina local
    apache1.vm.synced_folder "./web1", "/var/www/html"
    apache1.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        sudo systemctl enable apache2
        sudo systemctl start apache2
    SHELL
    end

    # Configuración de la segunda máquina (Servidor Apache 2)
config.vm.define "apache2" do |apache2|
    apache2.vm.box = "ubuntu/jammy64"
    apache2.vm.network "private_network", ip: "192.168.56.102"
    apache2.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
    end
# Directorio compartido en la máquina local
    apache2.vm.synced_folder "./web2", "/var/www/html"
    apache2.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        sudo systemctl enable apache2
        sudo systemctl start apache2
    SHELL
    end

    # Configuración de la tercera máquina (Servidor NGINX con balanceo de carga)
    config.vm.define "nginx" do |nginx|
        nginx.vm.box = "ubuntu/jammy64"
        nginx.vm.network "private_network", ip: "192.168.56.103"
        nginx.vm.provider "virtualbox" do |vb|
          vb.memory = "512"
          vb.cpus = 1
        end
        nginx.vm.provision "shell", inline: <<-SHELL
          sudo apt-get update
          sudo apt-get install -y nginx
          # Configuración de balanceo de carga
          sudo tee /etc/nginx/conf.d/load_balancer.conf > /dev/null <<EOF
    upstream backend {
        server 192.168.56.101;
        server 192.168.56.102;
    }
    
    server {
        listen 80;
    
        location / {
            proxy_pass http://backend;
        }
    }
    EOF
          sudo systemctl enable nginx
          sudo systemctl restart nginx
        SHELL
      end
    end