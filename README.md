Rullande Medkänsla
==================

Provision a fully configured Ubuntu 18.04 Bionic Beaver VM for development using Vagrant & Ansible

![GitHub tag (latest SemVer)](https://img.shields.io/github/v/tag/itnok/rullande-medkansla?sort=semver) ![Travis (.com) branch](https://img.shields.io/travis/com/itnok/rullande-medkansla/master)

:paw_prints: Getting Started
----------------------------

This `README.md` file is inside a folder that contains a `Vagrantfile` (hereafter this folder shall be called the [vagrant_root]), which tells Vagrant how to set up your virtual machine. This setup was created to work under [VMware Workstation Pro](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html) or [VMware Fusion Pro](https://www.vmware.com/my/products/fusion-pro.html), but with some minor changes to the `Vagrantfile` file it could be adapted to work with VirtualBox as well _(Aforementioned changed are not part of this repo, nor are supposed to be)_.

To use the `Vagrantfile`, you will need to have the following steps coverd first:

- Download and Install VMware _("Worstation Pro" for :penguin: or :diamond_shape_with_a_dot_inside: and "Fusion Pro" for :apple:)_
- Download and Install [Vagrant Plugin](https://www.vagrantup.com/vmware/index.html)
- Install [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
- Install the required Ansible Roles the provisioning Ansible Playbook relies upon with:
```
    $ ansible-galaxy role install --force -r requirements.yml
```

_(This operation should happend form the CLI from inside the repository directory)_

Once all these steps are done and no error is returned in the process, the Virtual Machine is ready to created and provisioned.

```
    $ vagrant up --provision
```

Once the new VM is up and running _(after `vagrant up` is complete and you're back at the command prompt)_, you can log into it via SSH if you'd like by typing in:

```
    $ vagrant ssh
```

If the VM is up and running and a new provisioning is needed to clean up any change interfering with the development environment from the host machine run:

```
    $ vagrant provision
```

To destroy the VM and completely remove it from the host machine use:

```
    $ vagrant destroy
```
