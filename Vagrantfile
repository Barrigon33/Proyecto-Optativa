# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # 1. DEFINICIÓN DEL SISTEMA (Basado en Ubuntu 22.04 LTS) [cite: 19]
  config.vm.box = "ubuntu/jammy64"
  
  # 2. IDENTIDAD Y RED
  config.vm.hostname = "servidor-webfusion"
  # Mapeo del puerto 8080 de tu Windows al 80 del contenedor WordPress
  config.vm.network "forwarded_port", guest: 80, host: 8080

  # 3. RECURSOS DE HARDWARE (VirtualBox)
  config.vm.provider "virtualbox" do |vb|
    vb.name = "Proyecto_WebFusion_Automatizado"
    vb.memory = "2048"
    vb.cpus = 2
  end

  # 4. APROVISIONAMIENTO AUTOMÁTICO (Instalación de Docker) [cite: 20, 23]
  config.vm.provision "shell", inline: <<-SHELL
    echo "Iniciando instalación automatizada de Docker..."
    apt-get update
    apt-get install -y ca-certificates curl gnupg
    
    # Añadir claves y repositorio oficial de Docker
    install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    chmod a+r /etc/apt/keyrings/docker.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
    
    # Instalar Docker Engine y Docker Compose
    apt-get update
    apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
    
    # Configurar permisos para que 'vagrant ssh' pueda usar docker sin sudo
    usermod -aG docker vagrant
    echo "Docker instalado con éxito."
  SHELL

  # 5. DESPLIEGUE DE CONTENEDORES (WordPress y Git) [cite: 21, 24, 25]
  config.vm.provision "shell", run: "always", inline: <<-SHELL
    echo "Desplegando contenedores mediante Docker Compose..."
    cd /vagrant
    if [ -f "docker-compose.yml" ]; then
      docker compose up -d
      echo "Entorno WordPress activo en http://localhost:8080"
    else
      echo "ERROR: No se encontró el archivo docker-compose.yml en la carpeta del proyecto."
    fi
  SHELL
end