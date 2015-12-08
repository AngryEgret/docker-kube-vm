Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.vm.box = "coreos-stable"
  config.vm.box_url = "http://stable.release.core-os.net/amd64-usr/766.4.0/coreos_production_vagrant.json"

  config.vm.define "kube-apiserver" do |api|
    File.open(File.join(File.dirname(__FILE__), 'services')).each do |line|
     port = line.split(/\s/).first.to_i
     api.vm.network "forwarded_port", guest: port, host: port
    end
    api.vm.network "private_network", ip: "172.31.255.254"
    api.vm.synced_folder "#{ENV['HOME']}/src", "/home/core/src", id: "core", :nfs => true, :mount_options => ['nolock,vers=3,udp'] 
    api.vm.synced_folder ".", "/home/core/kube-resources", id: "core", :nfs => true, :mount_options => ['nolock,vers=3,udp'] 
    api.vm.provider :virtualbox do |v|
      v.name = "coreos-vm"
      v.customize [
          "modifyvm", :id,
          "--name", "kube-apiserver",
          "--memory", 768,
          "--natdnshostresolver1", "on",
          "--cpus", 1,
      ]
    end
    CLOUD_CONFIG_API_PATH = File.join(File.dirname(__FILE__), "cloud-config.apiserver")
    api.vm.provision :file, :source => "#{CLOUD_CONFIG_API_PATH}", :destination => "/tmp/vagrantfile-user-data"
    api.vm.provision :shell, :inline => "mv /tmp/vagrantfile-user-data /var/lib/coreos-vagrant/", :privileged => true
    
  end
  
  CLOUD_CONFIG_KUBELET_PATH = File.join(File.dirname(__FILE__), "cloud-config.kubelet")
  
  config.vm.define "kubelet1" do |kubelet1|
    kubelet1.vm.network "private_network", ip: "172.31.255.100"
    kubelet1.vm.provider :virtualbox do |v|
      v.name = "coreos-vm"
      v.customize [
          "modifyvm", :id,
          "--name", "kubelet1",
          "--memory", 1024,
          "--natdnshostresolver1", "on",
          "--cpus", 1,
      ]
    end
    kubelet1.vm.provision :file, :source => "#{CLOUD_CONFIG_KUBELET_PATH}", :destination => "/tmp/vagrantfile-user-data"
    kubelet1.vm.provision :shell, :inline => "mv /tmp/vagrantfile-user-data /var/lib/coreos-vagrant/", :privileged => true    
  end
  
  config.vm.define "kubelet2", autostart: false do |kubelet2|
    kubelet2.vm.network "private_network", ip: "172.31.255.101"
    kubelet2.vm.provider :virtualbox do |v|
      v.name = "coreos-vm"
      v.customize [
          "modifyvm", :id,
          "--name", "kubelet2",
          "--memory", 1024,
          "--natdnshostresolver1", "on",
          "--cpus", 1,
      ]
    end
    kubelet2.vm.provision :file, :source => "#{CLOUD_CONFIG_KUBELET_PATH}", :destination => "/tmp/vagrantfile-user-data"
    kubelet2.vm.provision :shell, :inline => "mv /tmp/vagrantfile-user-data /var/lib/coreos-vagrant/", :privileged => true
  end
end
