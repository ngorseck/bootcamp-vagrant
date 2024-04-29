servers = [
  { :hostname => "node01", :ip => "192.168.1.10", :memory => "2048", :disks => 2 },
  { :hostname => "node02", :ip => "192.168.1.20", :memory => "2048", :disks => 2 },
  { :hostname => "node03", :ip => "192.168.1.30", :memory => "2048", :disks => 2 },
]
Vagrant.configure("2") do |config|
  config.vm.define "ansible" do |ansible|
    ansible.vm.box = "ubuntu/jammy64"
    ansible.vm.network "private_network", type: "static", ip: "192.168.99.10"
    ansible.vm.hostname = "ansible"
    ansible.vm.provider "virtualbox" do |v|
      v.name = "ansible"
      v.memory = 2048
      v.cpus = 2
    end
  end

  ram_client=2048
  cpu_client=2
  config.vm.define "client1" do |client|
    client.vm.box = "ubuntu/jammy64"
    client.vm.network "private_network", type: "static", ip: "192.168.99.11"
    client.vm.hostname = "client1"
    client.vm.provider "virtualbox" do |v|
      v.name = "client1"
      v.memory = ram_client
      v.cpus = cpu_client
    end
  end

  (1..3).each do |i|
    config.vm.define "Node-#{i}" do |node|
      node.vm.box = "centos/7"
      node.vm.network "private_network", ip: "192.168.100.2{i}"
      node.vm.synced_folder ".", "/vagrant", disabled: true

      node.vm.provider "virtualbox" do |vb|
        # Customize the amount of memory on the VM:
        vb.name   = "Name of your project - Node #{i}"
        vb.memory = "512"
        vb.cpus   = "1"
      end
    end
  end

    
  servers.each do |conf|
      config.vm.define conf[:hostname] do |node|
          node.vm.box = "centos/7"
          node.vm.hostname = conf[:hostname]
          node.vm.network "private_network", ip: conf[:ip]
          node.vm.provider "virtualbox" do |vb|
              vb.customize ['storagectl', :id, '--name', 'SATA Controller', '--portcount', conf[:disks]+1]
              (1..conf[:disks]).each do |x|
                  file_to_disk = './disk_'+conf[:hostname]+'_'+x.to_s()+'.vdi'
                  unless File.exist?(file_to_disk)
                      vb.customize ['createhd', '--filename', file_to_disk, '--size', 20 * 1024]
                  end
                  vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', x, '--device', 0, '--type', 'hdd', '--medium', file_to_disk]
              end
              vb.memory = conf[:memory]
          end
      end
  end
end
