nodes = [
  { :hostname => 'k8s-master',   :ip => '192.168.50.10', :ram => 2048, :box => 'CrunchyData/rocky8',  :forPort => 2100, :cidr => '192.168.50.0/24' } ,
  { :hostname => 'k8s-worker01',  :ip => '192.168.50.20', :ram => 1024, :box => 'CrunchyData/rocky8', :forPort => 2101 } ,
  { :hostname => 'k8s-worker02',  :ip => '192.168.50.30', :ram => 1024, :box => 'CrunchyData/rocky8', :forPort => 2102 }
]

Vagrant.configure("2") do |config|
  nodes.each do |node|
    config.vm.define node[:hostname] do |nodeconfig|
	  nodeconfig.vm.network "forwarded_port",  guest: 22, host: node[:forPort]  ## in case you are in a vpn, you can redirect
	  #nodeconfig.vm.synced_folder "tmp/", "/tmp/cluster"  ## sync folders. useful when some nodes need other nodes info
      nodeconfig.vm.box = node[:box]  ## change name that appear in the virtual box
      nodeconfig.vm.hostname = node[:hostname] ## hostname of the vm
      nodeconfig.vm.network :private_network, ip: node[:ip]  ## configure network according to variables node
      nodeconfig.vm.provider :virtualbox do |domain|
        domain.memory = node[:ram] ## ram memory
        domain.cpus = 2 ## cpus
		domain.name = node[:hostname] ## change name that appear in ther virtual box
	  nodeconfig.vm.provision "main-scripts", :type => "shell", :path => "scripts/main-scripts.sh", :args => "#{node[:ip]}"  ## Execute base script
	  nodeconfig.vm.provision "install-master", type: "shell", :path => "scripts/install-master.sh", :args => "#{node[:ip]} #{node[:cidr]}"  ## Execute master script
	  nodeconfig.vm.provision "install-master-metrics-server", type: "shell", :path => "scripts/install-master-metrics-server.sh" ##
	  nodeconfig.vm.provision "install-master-metal-lb", type: "shell", :path => "scripts/install-master-metal-lb.sh" ##
	  nodeconfig.vm.provision "install-master-helm", type: "shell", :path => "scripts/install-master-helm.sh" ##
	  nodeconfig.vm.provision "install-master-dashboard", type: "shell", :path => "scripts/install-master-dashboard.sh" ##
	  nodeconfig.vm.provision "install-worker", type: "shell", :path => "scripts/install-worker.sh" ## Execute workers script
	  #nodeconfig.vm.provision "install-master-ingress.", type: "shell", :path => "scripts/install-master-ingress.sh" ##
    nodeconfig.vm.provision "reboot", type: "shell", :path => "scripts/install-reboot.sh" ##
      end
    end
  end
end
