# -*- mode: ruby -*-
# vim: set ft=ruby :

$common = <<-'SCRIPT'
    yum install -y nfs-utils
    systemctl enable firewalld --now
SCRIPT

$make_server = <<-'SCRIPT'
    firewall-cmd --add-service="nfs3" --add-service="rpc-bind" --add-service="mountd" --permanent
    firewall-cmd --reload
    systemctl enable nfs --now
    mkdir -p /srv/share/upload
    chown -R nfsnobody:nfsnobody /srv/share
    chmod 0777 /srv/share/upload
    echo "/srv/share 192.168.57.11/32(rw,sync,root_squash)" >> /etc/exports
    exportfs -r
SCRIPT

$make_client = <<-'SCRIPT'
    echo "192.168.57.10:/srv/share/ /mnt nfs vers=3,proto=udp,noauto,x-systemd.automount 0 0" >> /etc/fstab
    systemctl daemon-reload
    systemctl restart remote-fs.target
    cd /mnt
SCRIPT

Vagrant.configure("2") do |config|
    config.vm.box = "centos/7"
    config.vm.box_version = "2004.01"
    config.vm.provider "virtualbox" do |v|
        v.memory = 1024
        v.cpus = 1
end
    config.vm.define "server" do |nfss|
        nfss.vm.network "private_network", ip: "192.168.57.10",
        virtualbox__intnet: "vboxnet1"
        nfss.vm.hostname = "server"
        nfss.vm.provision "shell", inline: $common
        nfss.vm.provision "shell", inline: $make_server
    end
    config.vm.define "client" do |nfsc|
        nfsc.vm.network "private_network", ip: "192.168.57.11",
        virtualbox__intnet: "vboxnet1"
        nfsc.vm.hostname = "client"
        nfsc.vm.provision "shell", inline: $common
        nfsc.vm.provision "shell", inline: $make_client
    end
end
