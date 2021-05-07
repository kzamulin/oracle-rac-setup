IMAGE_NAME = "oraclelinux/7"
BOX_URL = "https://oracle.github.io/vagrant-projects/boxes/oraclelinux/7.json"
N = 2

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 1024
        v.cpus = 1
    end
      
    config.vm.define "dnssrv" do |dnssrv|
        dnssrv.vm.box = IMAGE_NAME
        dnssrv.vm.box_url = BOX_URL
        dnssrv.vm.network "private_network", ip: "192.168.50.10", virtualbox__intnet: "intnet"
        dnssrv.vm.network "private_network", ip: "192.168.224.150"
        #dnssrv.vm.network "public_network", ip: "192.168.1.10", bridge: "Intel(R) Wi-Fi 6 AX201 160MHz"
        dnssrv.vm.hostname = "dnssrv"
        config.vm.provision "shell" do |s|
            ssh_prv_key = ""
            ssh_pub_key = ""
            if File.file?("#{Dir.home}/.ssh/id_rsa")
              ssh_prv_key = File.read("#{Dir.home}/.ssh/id_rsa")
              ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
            else
              puts "No SSH key found. You will need to remedy this before pushing to the repository."
            end
            s.inline = <<-SHELL
              systemctl stop firewalld
              systemctl disable firewalld
              if grep -sq "#{ssh_pub_key}" /home/vagrant/.ssh/authorized_keys; then
                echo "SSH keys already provisioned."
                exit 0;
              fi
              echo "SSH key provisioning."
              mkdir -p /home/vagrant/.ssh/
              mkdir -p /root/.ssh/
              touch /home/vagrant/.ssh/authorized_keys
              touch /root/.ssh/authorized_keys
              echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
              echo #{ssh_pub_key} >> /root/.ssh/authorized_keys
              chown -R vagrant:vagrant /home/vagrant
              echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
              sysctl -p
              yum install net-tools -y
              systemctl stop vboxadd-service.service
              systemctl disable vboxadd-service.service
              rm /usr/sbin/VBoxService
              timedatectl set-timezone Europe/Moscow
              exit 0
            SHELL
          end
    end

    config.vm.provider "virtualbox" do |v|
      v.memory = 4096
      v.cpus = 2
    end

    (1..N).each do |i|
        config.vm.define "oradb#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.box_url = BOX_URL
            node.vm.network "private_network", ip: "192.168.50.#{i + 10}", virtualbox__intnet: true
            node.vm.network "private_network", ip: "192.168.224.#{i + 150}"
            #node.vm.network "public_network", ip: "192.168.1.#{i +10}", bridge: "Intel(R) Wi-Fi 6 AX201 160MHz"
            node.vm.hostname = "node-#{i}"
            config.vm.provision "shell" do |s|
                ssh_prv_key = ""
                ssh_pub_key = ""
                if File.file?("#{Dir.home}/.ssh/id_rsa")
                  ssh_prv_key = File.read("#{Dir.home}/.ssh/id_rsa")
                  ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
                else
                  puts "No SSH key found. You will need to remedy this before pushing to the repository."
                end
                s.inline = <<-SHELL
                  systemctl stop firewalld
                  systemctl disable firewalld
                  if grep -sq "#{ssh_pub_key}" /home/vagrant/.ssh/authorized_keys; then
                    echo "SSH keys already provisioned."
                    exit 0;
                  fi
                  echo "SSH key provisioning."
                  mkdir -p /home/vagrant/.ssh/
                  mkdir -p /root/.ssh/
                  touch /home/vagrant/.ssh/authorized_keys
                  touch /root/.ssh/authorized_keys
                  echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
                  echo #{ssh_pub_key} >> /root/.ssh/authorized_keys
                  chown -R vagrant:vagrant /home/vagrant
                  echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
                  sysctl -p
                  yum install net-tools -y
                  systemctl stop vboxadd-service.service
                  systemctl disable vboxadd-service.service
                  rm /usr/sbin/VBoxService
                  timedatectl set-timezone Europe/Moscow
                  exit 0
                SHELL
              end
        end
    end
end