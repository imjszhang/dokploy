# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  config.vm.hostname = "dokploy-server"
    
  # 增加启动超时时间
  config.vm.boot_timeout = 1800
  
  # 端口转发
  config.vm.network "forwarded_port", guest: 80, host: 80    # HTTP
  config.vm.network "forwarded_port", guest: 443, host: 443   # HTTPS
  
  # 共享文件夹
  config.vm.synced_folder "./dokploy", "/home/vagrant/dokploy", create: true
  
  # 资源配置
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "4096"
    vb.cpus = 1
    vb.name = "dokploy-vm"
  end
  
  # 初始配置脚本
  config.vm.provision "shell", inline: <<-SHELL
    # 更新系统
    apt-get update
    apt-get upgrade -y
    
    # 安装必要的软件包
    apt-get install -y curl git apt-transport-https ca-certificates gnupg lsb-release
    
    # 安装Docker
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
    apt-get update
    apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
    
    # 将vagrant用户添加到docker组
    usermod -aG docker vagrant
    
    # 安装Docker Compose
    curl -SL https://github.com/docker/compose/releases/download/v2.18.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
    chmod +x /usr/local/bin/docker-compose        
    
    # 调整目录权限
    chown -R vagrant:vagrant /home/vagrant/dokploy
    chmod -R 755 /home/vagrant/dokploy
    
    # 创建并设置 /etc/dokploy 目录权限
    mkdir -p /etc/dokploy
    chown -R vagrant:vagrant /etc/dokploy
    chmod -R 755 /etc/dokploy

    # 设置 /etc/dokploy/traefik 目录权限
    mkdir -p /etc/dokploy/traefik
    chown -R vagrant:vagrant /etc/dokploy/traefik
    chmod -R 755 /etc/dokploy/traefik
    
    # 设置 /etc/dokploy/traefik/dynamic 目录权限
    mkdir -p /etc/dokploy/traefik/dynamic
    chown -R vagrant:vagrant /etc/dokploy/traefik/dynamic
    chmod -R 755 /etc/dokploy/traefik/dynamic

    # 安装Dokploy CLI
    curl -sSL https://dokploy.com/install.sh | sh
            
    # 启动服务
    cd /home/vagrant/dokploy
    docker-compose down
    docker-compose up -d
    
    # 等待服务启动
    echo "等待Dokploy服务启动..."
    sleep 30
    
    echo "Dokploy安装完成！可以通过以下地址访问："
    echo "Dokploy UI: http://dokploy.localhost"
    echo "Traefik Dashboard: http://traefik.localhost"
    
  SHELL
end 