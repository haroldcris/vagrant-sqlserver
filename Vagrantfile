# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrant virtual environments for SQL Server 2017 on Ubuntu Linux

confDir = $confDir ||= File.expand_path(File.dirname(__FILE__))
yamlPath = confDir + "/configuration.yaml"

Vagrant.configure(2) do |config|
  if File.exist? yamlPath then
      settings = YAML::load(File.read(yamlPath))
  else
      abort "Configuration file not found in #{yamlPath}"
  end

  config.vm.box_check_update = true
  config.vbguest.auto_update = true
  
  config.vm.provider "virtualbox" do |vb, override|
    vb.name = settings["vmName"] ||= "linux-mssql"
    vb.memory = settings["memory"] ||= "2096"
    vb.cpus = settings["cpus"] ||= 2
    
    # Do not use official Ubuntu box, broken in many ways https://bugs.launchpad.net/cloud-images/+bug/1569237
    override.vm.box = "bento/ubuntu-18.04"
    override.vm.network "private_network", type: "dhcp"

    # override.vm.network :forwarded_port, host: 2433, guest: 1433  # SQLServer
    # Map ports.
    if settings.include? 'ports'
        settings["ports"].each do |port|
            override.vm.network :forwarded_port, host: port["host"], guest: port["guest"]
        end
    end

    dbFolder = settings["dbPath"] ||= "."
    # override.vm.synced_folder dbFolder, "/var/opt/mssql/data/backup", disabled: false
    override.vm.synced_folder dbFolder, "/home/database", disabled: false
  end

  # scripts = [ "bootstrap.sh" ]
  #  scripts.each { |script|
  #   config.vm.provision :shell, privileged: false, :path => "./" << script
  # }

  config.vm.provision 'shell' do |s|
    s.path = './bootstrap.sh'
  end

end
