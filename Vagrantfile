IMAGE_NAME = "bento/ubuntu-18.04"
N = 1

Vagrant.configure("2") do |config|
    # config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 4096
        v.cpus = 4
        # v.disksize.size = '50GB'
    end
      
    config.vm.define "k8s-master" do |master|

        master.vm.provider "virtualbox" do |v|
            v.name = "k8s-master"
        end
        
        master.vm.box = IMAGE_NAME
        master.vm.hostname = "k8s-master"

        # master.vm.synced_folder ".", "/vagrant", disabled: true

        master.vm.network "private_network", ip: "192.168.50.10"
        # master.vm.network "public_network"
		
		master.vm.boot_timeout = 600
        
        master.vm.provision "ansible_local" do |ansible|
            ansible.playbook = "kubernetes-setup/master-playbook.yml"
            ansible.install_mode = "pip"
            # ansible.version = "2.9.12"
            ansible.extra_vars = {
                node_ip: "192.168.50.10",
            }
        end

    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|

            node.vm.provider "virtualbox" do |v|
              v.name = "node-#{i}"
            end

            node.vm.box = IMAGE_NAME
            node.vm.hostname = "node-#{i}"
            # node.vm.synced_folder ".", "/vagrant", disabled: true

            node.vm.network "private_network", ip: "192.168.50.#{i + 10}"
            # node.vm.network "public_network"
			
			node.vm.boot_timeout = 600
            
            node.vm.provision "ansible_local" do |ansible|
                ansible.playbook = "kubernetes-setup/node-playbook.yml"
                ansible.install_mode = "pip"
                # ansible.version = "2.9.12"
                ansible.extra_vars = {
                    node_ip: "192.168.50.#{i + 10}",
                }
            end
        end
    end
end