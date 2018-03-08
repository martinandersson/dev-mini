# -*- mode: ruby -*-
# vi: set ft=ruby :

CONFIGURATION = {
  machines: 'my-ubuntu',
  # https://github.com/martinanderssondotcom/box-ubuntu-budgie-17-x64
  box: 'pristine/ubuntu-budgie-17-x64',
  first_ip: '192.168.60.10',
  cpus: Etc.nprocessors,
  memory_mb: 4096
}



['vagrant-vbguest', 'vagrant-hostmanager'].reject{|p| Vagrant.has_plugin? p}.each do |p|
  if system "vagrant plugin install #{p}"
    exec 'vagrant ' + (ARGV.join ' ')
  else
    abort %Q.Failed to install "#{p}" plugin :'(.
  end
end


require 'psych'


Vagrant.configure('2') do |config|
  config.hostmanager.enabled = true
  config.hostmanager.include_offline = true
  
  config.vm.provider 'virtualbox' do |vb|
    vb.linked_clone = true
  end
end


def configure_machine options
  Vagrant.configure('2') do |config|
    name = options[:name]
    
    config.vm.define name do |machine|
      machine.vm.box = options[:box]
      ip = options[:ip]
      machine.vm.network 'private_network', ip: ip
      machine.vm.hostname = name
      
      machine.vm.provider('virtualbox') do |vb|
        vb.name = name + " (#{ip})"
        vb.cpus = options[:cpus]
        vb.memory = options[:memory]
        
        unless options[:gui].equal? nil
          vb.gui = options[:gui]
        end
      end
    end
  end
end


machine_names = []

ansible_groups = Hash.new { |hash, key|
  hash[key] = []
}


# Walk through all profiles and feed each machine to configure_machine()
(CONFIGURATION.is_a?(Hash) ? [CONFIGURATION] : CONFIGURATION).each do |profile|
  ip_parts = profile[:first_ip].rpartition '.'
  
  arr = *profile[:machines]
  arr.each.with_index do |name, index|
    configure_machine name: name,
      box: profile[:box],
      ip: ip_parts.first + '.' + (ip_parts.last.to_i + index).to_s,
      cpus: profile[:cpus],
      memory: profile[:memory_mb],
      gui: profile[:gui]
    
    machine_names << name
    
    grp = profile[:ansible_group]
    
    unless grp.equal? nil
      ansible_groups[grp] << name
    end
  end
end


# Setup the Ansible controller
# Some of it explained here: https://stackoverflow.com/a/46045193
controller = machine_names.last

private_keys = machine_names.grep_v(controller).reduce({}) do |hash, machine|
  hash[machine] = {
    old: "/vagrant/.vagrant/machines/#{machine}/virtualbox/private_key",
    new: '/home/vagrant/.ssh/private_key_' + machine }
  hash
end

Vagrant.configure('2') do |config|
  config.vm.define controller do |last|
    private_keys.each do |machine, key|
      config.vm.provision 'set Vagrant-exclusive access to private key for ' + machine, type: :shell, inline: <<~EOM
        cp #{key[:old]} #{key[:new]}
        chown vagrant:vagrant #{key[:new]}
        chmod 400 #{key[:new]}
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
      
      ansible.groups = ansible_groups
      
      # I use pip args only to stop Vagrant from tossing in the upgrade flag. Not
      # sure wtf that flag does. See:
      # https://www.vagrantup.com/docs/provisioning/ansible_local.html#options
      ansible.install_mode = :pip_args_only
      ansible.pip_args = 'ansible==2.4.3.0'
      
      roles_file = 'provisioning/requirements.yml'
      
      if File.exist?(roles_file) && !Psych.load_file(roles_file, nil).equal?(nil)
        ansible.galaxy_role_file = roles_file
        ansible.galaxy_roles_path = '/home/vagrant/.ansible/roles/'
        ansible.galaxy_command = 'ansible-galaxy install --role-file=%{role_file} --roles-path=%{roles_path}'
      end
    end
  end
end