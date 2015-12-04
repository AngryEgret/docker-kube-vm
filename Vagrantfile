Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.vm.box = "coreos-stable"
  config.vm.box_url = "http://stable.release.core-os.net/amd64-usr/766.4.0/coreos_production_vagrant.json"

  File.open(File.join(File.dirname(__FILE__), 'services')).each do |line|
   port = line.split(/\s/).first.to_i
   config.vm.network "forwarded_port", guest: port, host: port
  end

  config.vm.network "private_network", ip: "172.31.255.254"
  config.vm.synced_folder "#{ENV['HOME']}/src", "/home/core/src", id: "core", :nfs => true, :mount_options => ['nolock,vers=3,udp'] 
  config.vm.synced_folder ".", "/home/core/kube-resources", id: "core", :nfs => true, :mount_options => ['nolock,vers=3,udp'] 

  config.vm.provider :virtualbox do |v|
    v.name = "coreos-vm"
    v.customize [
        "modifyvm", :id,
        "--name", "coreos-vm",
        "--memory", 2048,
        "--natdnshostresolver1", "on",
        "--cpus", 4,
    ]
  end

  CLOUD_CONFIG_PATH = File.join(File.dirname(__FILE__), "cloud-config")
  config.vm.provision :file, :source => "#{CLOUD_CONFIG_PATH}", :destination => "/tmp/vagrantfile-user-data"
  config.vm.provision :shell, :inline => "mv /tmp/vagrantfile-user-data /var/lib/coreos-vagrant/", :privileged => true
end
