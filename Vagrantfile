IMAGE_NAME = "bento/ubuntu-18.04"
N = 6 

require 'rbconfig'
def is_windows
    RbConfig::CONFIG['host_os'] =~ /mswin|mingw|cygwin/
end

if is_windows
    provisioner = "ansible_local"
else
    provisioner = "ansible"
end

Vagrant.configure("2") do |config|
    # config.ssh.insert_key = false

    config.vm.define "nfs-server" do |nfs_server|

        nfs_server.vm.provider "virtualbox" do |v|
            v.cpus = 2
            v.memory = 2048
            # v.disksize.size = '50GB'
            v.name = "nfs_server"
        end

        nfs_server.vm.box = IMAGE_NAME
        nfs_server.vm.hostname = "nfs-server"

        # nfs_server.vm.synced_folder ".", "/vagrant", disabled: true

        nfs_server.vm.network "private_network", ip: "192.168.50.100"
        # nfs_server.vm.network "public_network"

		nfs_server.vm.boot_timeout = 600

        nfs_server.vm.provision provisioner do |ansible|

#            ansible.install_mode = "pip"
#            ansible.version = "2.9.12"

#            ansible.become = true
#            ansible.galaxy_role_file = "kubernetes-setup/requirements.yml"
#            ansible.galaxy_roles_path = "/etc/ansible/roles"
#            ansible.galaxy_command = "sudo ansible-galaxy install --role-file=%{role_file} --roles-path=%{roles_path} --force"

            ansible.playbook = "kubernetes-setup/nfs-server-playbook.yml"

            ansible.extra_vars = {
                node_ip: "192.168.50.100",
            }

        end

    end

    config.vm.define "k8s-master" do |master|

        master.vm.provider "virtualbox" do |v|
            v.cpus = 4
            v.memory = 4096
            # v.disksize.size = '50GB'
            v.name = "k8s-master"
        end

        master.vm.box = IMAGE_NAME
        master.vm.hostname = "k8s-master"

        # master.vm.synced_folder ".", "/vagrant", disabled: true

        master.vm.network "private_network", ip: "192.168.50.10"
        # master.vm.network "public_network"

        master.vm.boot_timeout = 600

        master.vm.provision provisioner do |ansible|
            ansible.playbook = "kubernetes-setup/master-playbook.yml"
 #           ansible.install_mode = "pip"
 #           ansible.version = "2.9.12"
            ansible.extra_vars = {
                node_ip: "192.168.50.10",
                nfs_server_ip: "192.168.50.100"
            }
            ansible.tags = ['nfs']
        end

    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|

            node.vm.provider "virtualbox" do |v|
                v.cpus = 4
                v.memory = 4096
                # v.disksize.size = '50GB'
                v.name = "node-#{i}"
            end

            node.vm.box = IMAGE_NAME
            node.vm.hostname = "node-#{i}"
            # node.vm.synced_folder ".", "/vagrant", disabled: true

            node.vm.network "private_network", ip: "192.168.50.#{i + 10}"
            # node.vm.network "public_network"

            node.vm.boot_timeout = 600

            node.vm.provision provisioner do |ansible|
                ansible.playbook = "kubernetes-setup/node-playbook.yml"
#                ansible.install_mode = "pip"
#                ansible.version = "2.9.12"
                ansible.extra_vars = {
                    node_ip: "192.168.50.#{i + 10}",
                    nfs_server_ip: "192.168.50.100"
                }
                ansible.tags = ['nfs']
            end
        end
    end

end
