# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"
$MEMSIZE=2048

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # The ordering of these 2 lines expresses a preference for a hypervisor
  config.vm.provider "virtualbox"
  config.vm.provider "vmware_fusion"

  config.ssh.forward_agent = false
  config.ssh.insert_key = false

  # Timeouts
  config.vm.boot_timeout = 900
  config.vm.graceful_halt_timeout=100

  # Use the Ansible playbook provision.yml to setup the virtual machines.
  config.vm.provision "ansible" do |ansible|
    ansible.inventory_path = "ansible.ini"
    ansible.playbook = "provision.yml"
    ansible.verbose = "vv"
    ansible.host_key_checking = "false"
  end

  # disable guest additions
  config.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true

  config.vm.define :dev,  primary: true do |dev_config|
    dev_config.vm.box = "chef/centos-6.6"
    dev_config.vm.box_url = "https://atlas.hashicorp.com/chef/boxes/centos-6.6"
    dev_config.vm.box_check_update = false
    # This host only network for use of Apache as a reverse proxy.
    dev_config.vm.network "private_network", ip: "192.168.10.16", :netmask => "255.255.255.0",  auto_config: true
    # To access this host use: 'vagrant ssh dev'
    dev_config.vm.network "forwarded_port", id: 'ssh', guest: 22, host: 2222, auto_correct: true
    # HTTPS for reverse proxy
    #dev_config.vm.network "forwarded_port", guest: 443, host: 8443, auto_correct: true

    dev_config.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", "#$MEMSIZE", "--natnet1", "172.16.1/24"]
      vb.customize ["modifyvm", :id, "--ioapic", "on"  ]
      vb.name = "dev"
      vb.gui = false
    end

    dev_config.vm.provider "vmware_fusion" do |vmware|
      vmware.gui = false
      vmware.vmx["memsize"] = "#$MEMSIZE"
      vmware.vmx["numvcpus"] = "2"
    end
  end

  config.vm.define :target, autostart: true do |target_config|
    target_config.vm.box = "dockpack/centos6"
    target_config.vm.box_url = "https://atlas.hashicorp.com/dockpack/boxes/centos6"
    target_config.vm.box_check_update = false
    target_config.vm.network "private_network", ip: "192.168.10.18", :netmask => "255.255.255.0",  auto_config: true
    target_config.vm.network "forwarded_port", id: 'ssh', guest: 22, host: 2223, auto_correct: true
    target_config.vm.network "forwarded_port", guest: 8080, host: 8080, auto_correct: true

    target_config.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", "#$MEMSIZE", "--natnet1", "172.16.1/24"]
      vb.gui = false
      vb.name = "target"
    end

    target_config.vm.provider "vmware_fusion" do |vmware|
      vmware.gui = false
      vmware.vmx["memsize"] = "#$MEMSIZE"
      vmware.vmx["numvcpus"] = 2
    end
  end

  config.vm.define :testclient, autostart: false do |testclient_config|
    testclient_config.vm.box = "ubuntu14"
    testclient_config.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"
    testclient_config.vm.network "private_network", ip: "192.168.10.20", :netmask => "255.255.255.0",  auto_config: true
    testclient_config.vm.network "forwarded_port", id: 'ssh', guest: 22, host: 2224, auto_correct: true
    testclient_config.vm.network :forwarded_port, guest:8000, host:8000
    testclient_config.vm.provider "vmware_fusion" do |vmware|
      vmware.vmx["memsize"] = "#$MEMSIZE"
      vmware.vmx["numvcpus"] = "2"
    end
    testclient_config.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", "#$MEMSIZE", "--natnet1", "172.16.1/24"]
      vb.gui = true
      vb.name = "testclient"
    end
    testclient_config.vm.provider "vmware_fusion" do |vmware|
      vmware.gui = false
      vmware.vmx["memsize"] = "#$MEMSIZE"
      vmware.vmx["numvcpus"] = 2
    end
  end

  config.vm.define :windows, autostart: false do |windows_config|
    windows_config.vm.box = "ferhaty/win7ie10winrm"
    windows_config.winrm.username = 'IEuser'
    windows_config.winrm.password = 'Passw0rd!'
    windows_config.vm.communicator = "winrm"
    windows_config.vm.box_url = "https://atlas.hashicorp.com/ferhaty/boxes/win7ie10winrm"
    windows_config.vm.network :private_network, ip: "192.168.10.40"
    windows_config.vm.network :forwarded_port, guest: 3389, host: 3389, id: "rdp", auto_correct: true
    windows_config.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", "#$MEMSIZE", "--natnet1", "172.16.1/24"]
      vb.gui = true
      vb.name = "windows"
    end
  end
end
