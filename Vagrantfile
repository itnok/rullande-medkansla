# -*- mode: ruby -*-
# vi: set ft=ruby :

### configuration parameters ###
# Vagrant API version to use
VAGRANTFILE_API_VERSION = "2"
# Vagrant box name (used for hostname too)
VAGRANT_BOX_NAME = "rullandemedkansla"
# Vagrant base box to use
VAGRANT_BOX_OS = "generic/ubuntu1804"
# amount of RAM for Vagrant box
VAGRANT_RAM_MB = "8192"
# number of CPUs for Vagrant box
VAGRANT_CPUS = "4"
# which IP address will be assigned to the box
VAGRANT_IP_ADDR = "10.101.210.123"
# which host port to forward box SSH port to
LOCAL_SSH_PORT = "22022"
### /configuration parameters ###


###
### Probably NO NEED to touch anything below this point!
###

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.ssh.username = "vagrant"
    config.ssh.host = "127.0.0.1"
    config.ssh.guest_port = 22
    config.ssh.insert_key = false

    # Vagrant boot needs more time on AppVeyor.
    # See: https://help.appveyor.com/discussions/problems/1247-vagrant-not-working-inside-appveyor
    config.vm.boot_timeout = 1800

    # Prevent SharedFoldersEnableSymlinksCreate errors.
    config.vm.synced_folder ".", "/vagrant", disabled: true

    # Set the name of the VM.
    # See: http://stackoverflow.com/a/17864388/100134
    config.vm.define VAGRANT_BOX_NAME do |box|
        box.vm.box = VAGRANT_BOX_OS
        box.vm.hostname = VAGRANT_BOX_NAME
        box.vm.network :private_network, ip: VAGRANT_IP_ADDR
        box.vm.network :forwarded_port, guest: 22, host: LOCAL_SSH_PORT, id: "ssh", auto_correct: true

        box.vm.provider :vmware_desktop do |v|
            v.gui = true
            v.vmx["memsize"] = VAGRANT_RAM_MB
            v.vmx["numvcpus"] = VAGRANT_CPUS
            v.vmx["mks.enable3d"] = true
        end

        config.vm.provider :virtualbox do |v|
            v.name = VAGRANT_BOX_NAME
            v.memory = VAGRANT_RAM_MB
            v.cpus = VAGRANT_CPUS
            # Vagrant needs this config on AppVeyor to spin up correctly
            # See: https://help.appveyor.com/discussions/problems/1247-vagrant-not-working-inside-appveyor
            v.customize ["modifyvm", :id, "--nictype1", "Am79C973"]
            v.customize ['modifyvm', :id, '--cableconnected1', 'on']
        end

        config.vm.provider :libvirt do |v|
            v.memory = VAGRANT_RAM_MB
            v.cpus = VAGRANT_CPUS
        end

    end

    # Ansible provisioner.
    config.vm.provision "ansible" do |ansible|
        ansible.compatibility_mode = "2.0"
        ansible.playbook = "provisioning/playbook.yml"
        ansible.become = true
    end

end
