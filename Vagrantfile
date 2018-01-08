# -*- mode: ruby -*-
# vi: set ft=ruby :

# See README.md
CONFIGURATION = {
  machine_names: ['my-ubuntu'],
  ip_start: '192.168.60.',
  
  master_acts_as_slave: false,
  master_box: 'pristine/ubuntu-budgie-17-x64',
  master_cpus: Etc.nprocessors,
  master_memory_mb: 4096,
  
  install_ansible_roles: false,
  
  slave_box: 'mhubbard/centos7',
  slave_cpus: 2,
  slave_memory_mb: 512
}



['vagrant-vbguest', 'vagrant-hostmanager'].reject{|p| Vagrant.has_plugin? p}.each do |p|
  if system "vagrant plugin install #{p}"
    exec 'vagrant ' + (ARGV.join ' ')
  else
    abort %Q.Failed to install "#{p}" plugin :'(.
  end
end

require 'date'

Vagrant.configure('2') do |config|
  config.hostmanager.enabled = true
  config.hostmanager.include_offline = true
  
  C = CONFIGURATION
  
  # General stuff
  config.vm.provider 'virtualbox' do |vb|
    vb.linked_clone = true
    vb.cpus = C[:slave_cpus]
    vb.memory = C[:slave_memory_mb]
  end
  
  # Setup baseline
  C[:machine_names].each.with_index do |name, index|
    config.vm.define name do |machine|
      ip = C[:ip_start] + (10 + index).to_s
      
      machine.vm.box = C[:slave_box]
      
      machine.vm.network 'private_network', ip: ip
      machine.vm.hostname = name
      
      machine.vm.provider('virtualbox') { |vb|
        vb.name = name + " [#{ip}] (saw light #{Date.today})"
      }
    end
  end
  
  # Maybe override some stuff for master
  unless C[:master_acts_as_slave]
    config.vm.define C[:machine_names].first do |master|
      master.vm.box = C[:master_box]
      
      master.vm.provider 'virtualbox' do |vb|
        vb.gui = true
        vb.cpus = C[:master_cpus]
        vb.memory = C[:master_memory_mb]
      end
    end
  end
  
  # Setup Ansible controller
  # (some of it explained here: https://stackoverflow.com/a/46045193)
  controller = C[:machine_names].last
  
  private_keys = C[:machine_names].grep_v(controller).reduce({}) do |hash, machine|
    hash[machine] = {
      old: "/vagrant/.vagrant/machines/#{machine}/virtualbox/private_key",
      new: '/home/vagrant/.ssh/private_key_' + machine }
    hash
  end
  
  config.vm.define controller do |last|
    private_keys.each do |machine, key|
      config.vm.provision 'set vagrant-exclusive access to private key for ' + machine, type: :shell, inline: <<~EOM
        if [[ -f #{key[:new]} ]] ; then
          echo 'Key already setup :)'
          exit
        fi
        
        cp #{key[:old]} #{key[:new]}
        chown vagrant:vagrant #{key[:new]}
        chmod 400 #{key[:new]}
      EOM
    end
    
    if C[:install_ansible_roles]
      config.vm.provision 'preemptively give others write access to /etc/ansible/roles', type: :shell, inline: <<~'EOM'
        mkdir /etc/ansible/roles -p
        chmod o+w /etc/ansible/roles
      EOM
    end
    
    config.vm.provision 'ansible', type: :ansible_local do |ansible|
      ansible.playbook = 'provisioning/playbook.yml'
      ansible.limit = 'all'
      
      ansible.host_vars = {}
      private_keys.each do |machine, key|
        ansible.host_vars[machine] = {
          'ansible_ssh_private_key_file' => key[:new],
          'ansible_ssh_extra_args' => '-o StrictHostKeyChecking=no'
        }
      end
      
      ansible.groups = {}
      ansible.groups['slaves'] = C[:machine_names].drop 1
      
      if C[:master_acts_as_slave]
        ansible.groups['slaves'] << C[:machine_names].first
      else
        ansible.groups['master'] = [ C[:machine_names].first ]
      end
      
      # I use pip args only to stop Vagrant from tossing in the upgrade flag. Not
      # sure wtf that flag does. See:
      # https://www.vagrantup.com/docs/provisioning/ansible_local.html#options
      ansible.install_mode = :pip_args_only
      ansible.pip_args = 'ansible==2.4.1.0'
      
      if C[:install_ansible_roles]
        ansible.galaxy_role_file = 'provisioning/requirements.yml'
        ansible.galaxy_roles_path = '/etc/ansible/roles'
        ansible.galaxy_command = 'ansible-galaxy install --role-file=%{role_file} --roles-path=%{roles_path}'
      end
    end
  end
end