IMAGE_NAME = "ubuntu/focal64"
N = 2

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false
    config.vm.box = IMAGE_NAME
    config.vm.synced_folder '.', '/vagrant', disabled: true
      
    config.vm.define "controlplane" do |master|
        master.vm.hostname = "controlplane"
    	master.vm.provider "virtualbox" do |v|
          v.memory = 4096
          v.cpus = 2
    	end
        master.vm.network "private_network", ip: "10.11.12.30"
        master.vm.network :forwarded_port, guest: 22, host: 2330, id: 'ssh'
	(30000..30010).each do |p|
	  master.vm.network :forwarded_port, guest: p, host: p
        end
        master.vm.provision "ansible" do |ansible|
            ansible.playbook = "k8s-install/master-playbook.yml"
            ansible.extra_vars = {
                node_ip: "10.11.12.30",
            }
        end
    end

    (1..N).each do |i|
        config.vm.define "node#{i}" do |node|
            node.vm.hostname = "node#{i}"
    	    node.vm.provider "virtualbox" do |v|
              v.memory = 4096
              v.cpus = 2
    	    end
            node.vm.network "private_network", ip: "10.11.12.#{i + 30}"
            node.vm.network :forwarded_port, guest: 22, host: "23#{i + 30}", id: 'ssh'
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "k8s-install/node-playbook.yml"
                ansible.extra_vars = {
                    node_ip: "10.11.12.#{i + 30}",
                }
            end
        end
    end
end
