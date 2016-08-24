# -*- mode: ruby -*-
# # vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

num_masters = 1
num_workers = 1
num_nodes = num_masters + num_workers
subnet = "192.168.9"
ip_range_start = 11

vm_gui = false
vm_cpus = 4
vm_memory = 2048

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

  config.vm.box = "centos/7"

  config.ssh.insert_key = false
  
  config.vm.provider :virtualbox do |vb|
    vb.check_guest_additions = false
    vb.functional_vboxsf     = false
    vb.gui = vm_gui
    vb.memory = vm_memory
    vb.cpus = vm_cpus
  end

  num_nodes.times do |i|
    vm_name = "%s-%02d" % ["node", i+1]
    config.vm.define vm_name, primary: i==0 do |config|
    
      config.vm.hostname = vm_name
      
      ip = "#{subnet}.#{ip_range_start+i}"
      config.vm.network "private_network", ip: ip

      # Vagrant's "change host name" capability maps hostname to loopback, conflicting with mesos (and hostmanager).
      # We must repair /etc/hosts
      config.vm.provision "shell", inline: "sed -ri 's/127\\.0\\.0\\.1\\s+#{vm_name}\\s(.*)/127.0.0.1   \\1/' /etc/hosts", :privileged => true
      config.vm.provision "shell", inline: "echo #{ip}   #{vm_name} | tee -a /etc/hosts", :privileged => true

    end
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "site.yml"
    ansible.inventory_path = "staging"
  end
      
end
