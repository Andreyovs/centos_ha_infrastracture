# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :mon => {
             :box_name => "centos/8",
             :net => [
                      {ip: '192.168.10.2', adapter: 3, netmask: "255.255.255.0"},
                     ],
            },
  :sqlnode1 => {
             :box_name => "centos/8",
             :net => [
                      {ip: '192.168.10.31', adapter: 3, netmask: "255.255.255.0"},
                     ],
            },
  :sqlnode2 => {
             :box_name => "centos/8",
             :net => [
                      {ip: '192.168.10.32', adapter: 3, netmask: "255.255.255.0"},
                     ],
            },
  :sqlnode3 => {
             :box_name => "centos/8",
             :net => [
                      {ip: '192.168.10.33', adapter: 3, netmask: "255.255.255.0"},
                     ],
            },
  :frontend1 => {
             :box_name => "centos/8",
             :net => [
                      {ip: '192.168.10.10', adapter: 3, netmask: "255.255.255.0"},
                     ],
            },
  :frontend2 => {
              :box_name => "centos/8",
              :net => [
                        {ip: '192.168.10.11', adapter: 3, netmask: "255.255.255.0"},
                        ],
               },
  :haproxy1 => {
             :box_name => "centos/8",
             :net => [
                      {ip: '192.168.10.101', adapter: 3, netmask: "255.255.255.0"},
                     ],
            },
  :haproxy2 => {
             :box_name => "centos/8",
             :net => [
                      {ip: '192.168.10.102', adapter: 3, netmask: "255.255.255.0"},
                     ],
            },
          }


etc_hosts = "127.0.0.1\tlocalhost\n"
counter = 0

MACHINES.each do |hostname,config|  
  config[:net].each do |ip|
      etc_hosts=etc_hosts+ip[:ip]+"\t"+hostname.to_s+"\n"
  end
end

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    config.vm.define boxname do |box|
        box.vm.box_check_update = false
        box.vm.box_version = "2011.0"
        box.vm.box = boxconfig[:box_name]
        box.vm.host_name = boxname.to_s
        box.ssh.insert_key = false
        box.ssh.forward_agent = true
        box.ssh.username = "vagrant"
        boxconfig[:net].each do |ipconf|
          box.vm.network "private_network", ipconf
        end
        box.vm.synced_folder '.', '/vagrant'
        box.vm.provider "virtualbox" do |v|
          v.memory = 1024
          v.cpus = 2
        end
        box.vm.provision "file", source: "~/.vagrant.d/insecure_private_key", destination: "~/.ssh/id_rsa" 
        box.vm.provision "updates", type:"shell", inline: <<-SHELL
          mkdir -p /root/.ssh;  
          cat /home/vagrant/.ssh/authorized_keys > /root/.ssh/authorized_keys
          cat /home/vagrant/.ssh/authorized_keys > /root/.ssh/id_rsa.pub
          cat /home/vagrant/.ssh/authorized_keys > /home/vagrant/.ssh/id_rsa.pub
          chmod 700 /root/.ssh
          cp /home/vagrant/.ssh/id_rsa /root/.ssh/id_rsa
          chmod 600 /root/.ssh/id_rsa
         
        SHELL
        box.vm.provision "hosts",after:"updates", type: "shell" , run: "always" do |shell|
          shell.inline = 'echo -e "$1" > /etc/hosts'
          shell.args = [etc_hosts]
        end
        
        box.vm.provision "ansible" , after: "hosts" do |ansible|
          ansible.verbose = "vvv"
          ansible.playbook = "./scripts/playbook.yml"
          ansible.host_key_checking = "False"
          ansible.force_remote_user = "vagrant"
          ansible.raw_arguments = [
               "--private-key=~/.vagrant.d/insecure_private_key"]
        end
    end
  end
end
