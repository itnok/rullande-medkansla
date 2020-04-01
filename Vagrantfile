# -*- mode: ruby -*-
# vi: set ft=ruby :

### configuration parameters ###
# Vagrant default home directory
VAGRANT_HOME = ENV["VAGRANT_HOME"] || "~/.vagrant.d"
# Vagrant default provider
VAGRANT_DEFAULT_PROVIDER = ENV["VAGRANT_DEFAULT_PROVIDER"] || "vmware_desktop"
# Vagrant API version to use
VAGRANTFILE_API_VERSION = "2"
# Vagrant box username
VAGRANT_BOX_USER = "v0vten"
# Vagrant box name (used for hostname too)
VAGRANT_BOX_NAME = "rullandemedkansla"
# Vagrant base box to use
VAGRANT_BOX_OS = "itnok/Ubuntu1804"
# amount of RAM for Vagrant box
VAGRANT_RAM_MB = ENV["VAGRANT_RAM_MB"] || "8192"
# number of CPUs for Vagrant box
VAGRANT_CPUS = ENV["VAGRANT_CPUS"] || "4"
# which IP address will be assigned to the box
VAGRANT_DHCP = ENV["VAGRANT_DHCP"] || "NO"
VAGRANT_IP_ADDR_BASE = "10.101.210."
VAGRANT_IP_ADDR = VAGRANT_IP_ADDR_BASE + "2"
# which host port to forward box SSH port to
LOCAL_SSH_PORT = "22022"
### /configuration parameters ###


###
### Probably NO NEED to touch anything below this point!
###

ENV["VAGRANT_DEFAULT_PROVIDER"] = VAGRANT_DEFAULT_PROVIDER

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.ssh.username = VAGRANT_BOX_USER
    config.ssh.host = "127.0.0.1"
    config.ssh.guest_port = 22
    config.ssh.insert_key = false

    # Vagrant boot needs more time on AppVeyor.
    # See: https://help.appveyor.com/discussions/problems/1247-vagrant-not-working-inside-appveyor
    config.vm.boot_timeout = 1800

    # Prevent SharedFoldersEnableSymlinksCreate errors.
    config.vm.synced_folder ".", "/vagrant", disabled: true

    if VAGRANT_DHCP == "YES"
        config.vm.network :private_network, :type => "dhcp",
            :netmask => "255.255.255.252", :libvirt__netmask => "255.255.255.252",
            :libvirt__network_address => VAGRANT_IP_ADDR_BASE + "0",
            :libvirt__dhcp_enabled => true,
            :libvirt__dhcp_start => VAGRANT_IP_ADDR,
            :libvirt__dhcp_stop => VAGRANT_IP_ADDR
    else
        # LibVirt appears to be incapable of understanding the concept of static IP
        # Using a very restrictive netmask solves the problem...
        config.vm.network :private_network, ip: VAGRANT_IP_ADDR,
            :netmask => "255.255.255.252", :libvirt__netmask => "255.255.255.252"
    end

    config.vm.network :forwarded_port,
        guest: 22, host: LOCAL_SSH_PORT,
        id: "ssh", auto_correct: true

    # Set the name of the VM.
    # See: http://stackoverflow.com/a/17864388/100134
    config.vm.define VAGRANT_BOX_NAME do |box|
        box.vm.box = VAGRANT_BOX_OS
        box.vm.hostname = VAGRANT_BOX_NAME

        box.vm.provider :vmware_desktop do |v|
            v.gui = true
            v.whitelist_verified = true
            v.vmx["memsize"] = VAGRANT_RAM_MB
            v.vmx["numvcpus"] = VAGRANT_CPUS
            v.vmx["mks.enable3d"] = true
        end

        config.vm.provider :virtualbox do |v|
            v.memory = VAGRANT_RAM_MB
            v.cpus = VAGRANT_CPUS
            # Vagrant needs this config on AppVeyor to spin up correctly
            # See: https://help.appveyor.com/discussions/problems/1247-vagrant-not-working-inside-appveyor
            v.customize ["modifyvm", :id, "--nictype1", "Am79C973"]
            v.customize ['modifyvm', :id, "--cableconnected1", "on"]
        end

        config.vm.provider :libvirt do |v|
            v.uri = "qemu:///system"
            v.host = VAGRANT_IP_ADDR
            v.username = VAGRANT_BOX_USER
            v.driver = "kvm"
            v.id_ssh_key_file = File.expand_path(VAGRANT_HOME + "/insecure_private_key")
            v.connect_via_ssh = true
            v.memory = VAGRANT_RAM_MB
            v.cpus = VAGRANT_CPUS
            v.video_vram = 256
            v.nic_model_type = "rtl8139"
            v.graphics_ip = "localhost"
            v.graphics_type = "vnc"
            v.graphics_port = "59666"
            v.mgmt_attach = true
        end

    end

    # Ansible provisioner.
    config.vm.provision "ansible" do |ansible|
        ansible.compatibility_mode = "2.0"
        ansible.playbook = "provisioning/playbook.yml"
        ansible.become = true
    end

end

