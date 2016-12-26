# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.box_check_update = false
  config.vm.network "private_network", ip: "192.168.33.60"
  config.vm.hostname = "aws-lambda.dev"

  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--memory", "1024", "--cpus", "2", "--ioapic", "on"]
  end

  %w(
    aws-lambda-sample
    aws-serverless-prototype
  ).each do |dir|
    config.vm.synced_folder "../#{dir}", "/home/vagrant/#{dir}" if  File.exist?("../#{dir}")
  end

  config.vm.provision "ansible" do |ansible|
    ansible.limit = "all"
    ansible.inventory_path = "hosts"
    ansible.playbook = "site.yml"
  end
end
