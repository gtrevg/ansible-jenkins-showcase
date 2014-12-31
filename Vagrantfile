# vi: set ft=ruby :
#

VAGRANTFILE_API_VERSION = '2'

boxes = [
  {
    :name => :jenkins,
    :book => 'jenkins',
  },
  {
    :name => :zabbix,
    :book => 'zabbix',
  },


]

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.hostmanager.enabled      = true
  config.hostmanager.include_offline  = true
  config.hostmanager.manage_host   = true
  config.hostmanager.ignore_private_ip = false
  config.vm.provision :hostmanager
  config.ssh.insert_key = false

  #config.ssh.insert_key = false
  #
  boxes.each do |opts|
    config.vm.define opts[:name] do |machine|

      machine.vm.provider :linode do |provider, override|
        override.ssh.private_key_path = "vagrant-keys/vagrant"
        override.vm.box = opts[:name].to_s
        override.vm.box_url = "https://github.com/displague/vagrant-linode/raw/master/box/linode.box"

        provider.token = ENV['linode_key']
        provider.distribution = 'CentOS 6'
        provider.datacenter = 'london'
        provider.plan = 'Linode 1024'
        # provider.planid = <int>
        # provider.paymentterm = <*1*,12,24>
        provider.datacenterid = 7
        #provider.image = "CentOS 7"
        # provider.imageid = <int>
        provider.private_networking = true
        # provider.stackscript = <string>
        # provider.stackscriptid = <int>
        # provider.distributionid = <int>
      end

      config.vm.synced_folder ".", "/vagrant", type: "rsync",
        rsync__exclude: [".git",
                        "vendor",
                        "videos",
                        "vagrant-keys",
                        "roles",
                        "library",
                        "group_vars"]

      config.vm.provision :ansible do |ansible|
        if defined? ENV['TAGS']
          ansible.tags = ENV['TAGS']
        end
        if defined? ENV['START_AT_TASK']
          ansible.start_at_task = ENV['START_AT_TASK']
        end

        #ansible.verbose = 'vvv'
        ansible.sudo = true
        ansible.playbook = "vagrant-#{opts[:book]}.yml"
        ansible.inventory_path= 'vagrant'
        ansible.vault_password_file = 'vagrant-vault'
        ansible.limit = opts[:name].to_s

        ansible.extra_vars = {
          'deploy_environment'    => 'vagrant',
        }
      end
    end
  end
end
# -*- mode: ruby -*-
