# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'

vagrant_config = YAML.load_file("devstack/provisioning/virtualbox.conf.yml")

Vagrant.configure(2) do |config|
  config.vm.box = vagrant_config['box']

  if Vagrant.has_plugin?("vagrant-cachier")
    # Configure cached packages to be shared between instances of the same base box.
    # More info on http://fgrehm.viewdocs.io/vagrant-cachier/usage
    config.cache.scope = :box
  end

  #config.vm.synced_folder ".", "/home/vagrant/networking-ovn", type: "nfs"

  # Bring up the Devstack controller node on Virtualbox
  config.vm.define "devstack_controller" do |devstack_controller|
    devstack_controller.vm.host_name = vagrant_config['devstack_controller']['host_name']
    devstack_controller.vm.network "private_network", ip: vagrant_config['devstack_controller']['ip']
    devstack_controller.vm.provision "shell", path: "devstack/provisioning/setup-base.sh", privileged: false
    devstack_controller.vm.provision "shell", path: "devstack/provisioning/setup-controller.sh", privileged: false

    config.vm.provider "virtualbox" do |vb|
       vb.memory = vagrant_config['devstack_controller']['memory']
       vb.cpus = vagrant_config['devstack_controller']['cpus']
    end
  end

  # Bring up the Devstack compute node on Virtualbox
  config.vm.define "devstack_compute" do |devstack_compute|
    devstack_compute.vm.host_name = vagrant_config['devstack_compute']['host_name']
    devstack_compute.vm.network "private_network", ip: vagrant_config['devstack_compute']['ip']
    devstack_compute.vm.provision "shell", path: "devstack/provisioning/setup-base.sh", privileged: false
    devstack_compute.vm.provision "shell", path: "devstack/provisioning/setup-compute.sh", privileged: false, :args => "#{vagrant_config['devstack_controller']['ip']}"
    config.vm.provider "virtualbox" do |vb|
       vb.memory = vagrant_config['devstack_compute']['memory']
       vb.cpus = vagrant_config['devstack_compute']['cpus']
    end
  end
end
