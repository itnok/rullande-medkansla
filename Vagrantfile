# -*- mode: ruby -*-
# vi: set ft=ruby :

### configuration parameters ###
# Vagrant API version to use
VAGRANTFILE_API_VERSION = "2"
# Vagrant box name (used for hostname too)
VAGRANT_BOX_NAME = "rullandemedkansla"
# Vagrant base box to use
VAGRANT_BOX_OS = "itnok/Ubuntu1804"
# amount of RAM for Vagrant box
VAGRANT_RAM_MB = "8192"
# number of CPUs for Vagrant box
VAGRANT_CPUS = "4"
# which IP address will be assigned to the box
VAGRANT_IP_ADDR_BASE = "10.101.210."
VAGRANT_IP_ADDR = VAGRANT_IP_ADDR_BASE + "123"
# which host port to forward box SSH port to
LOCAL_SSH_PORT = "22022"
### /configuration parameters ###


###
### Probably NO NEED to touch anything below this point!
###

# Allows to set some parameters via environment variables
# Mostly usefull to deal with CI where resource are constrained (e.g. Travis CI)
if ENV["VAGRANT_DEFAULT_PROVIDER"] == ""
    ENV["VAGRANT_DEFAULT_PROVIDER"] = "vmware_desktop"
end

if ENV["VAGRANT_CPUS"] == ""
    ENV["VAGRANT_CPU"] = VAGRANT_CPUS
end

if ENV["VAGRANT_RAM_MB"] == ""
    ENV["VAGRANT_RAM_MB"] = VAGRANT_RAM_MB
end


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.ssh.username = "v0vten"
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
        if ENV["VAGRANT_DHCP"] == "YES"
            box.vm.network :private_network,
                :type => "dhcp",
                :libvirt__network_address => VAGRANT_IP_ADDR_BASE + "0"
        else
            box.vm.network :private_network, ip: VAGRANT_IP_ADDR
            box.vm.network :forwarded_port, guest: 22, host: LOCAL_SSH_PORT, id: "ssh", auto_correct: true
        end

        box.vm.provider :vmware_desktop do |v|
            v.gui = true
            v.vmx["memsize"] = ENV["VAGRANT_RAM_MB"]
            v.vmx["numvcpus"] = ENV["VAGRANT_CPUS"]
            v.vmx["mks.enable3d"] = true
            v.vmx["ethernet1.pcislotnumber"] = "38"
        end

        config.vm.provider :virtualbox do |v|
            v.memory = ENV["VAGRANT_RAM_MB"]
            v.cpus = ENV["VAGRANT_CPUS"]
            # Vagrant needs this config on AppVeyor to spin up correctly
            # See: https://help.appveyor.com/discussions/problems/1247-vagrant-not-working-inside-appveyor
            v.customize ["modifyvm", :id, "--nictype1", "Am79C973"]
            v.customize ['modifyvm', :id, "--cableconnected1", "on"]
        end

        config.vm.provider :libvirt do |v|
            v.memory = ENV["VAGRANT_RAM_MB"]
            v.cpus = ENV["VAGRANT_CPUS"]
            v.nic_model_type = "rtl8139"
            v.graphics_type = "vnc"
            v.graphics_port = "5900"
        end

    end

    # Ansible provisioner.
    config.vm.provision "ansible" do |ansible|
        ansible.compatibility_mode = "2.0"
        ansible.playbook = "provisioning/playbook.yml"
        ansible.become = true
    end

end
