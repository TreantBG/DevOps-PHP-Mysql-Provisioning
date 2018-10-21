numworkers = 2
vmmemory = 512
numcpu = 1
manager_ip = "192.168.10.2"
instances = []
Vagrant.require_version ">= 1.8.4"

(1..numworkers).each do |n| 
  instances.push({:name => "worker#{n}", :ip => "192.168.10.#{n+2}", :number => "#{n}"})
end

File.open("./hosts", 'w') { |file| 
  instances.each do |i|
    file.write("#{i[:ip]} #{i[:name]} #{i[:name]}\n")
  end
}

Vagrant.configure("2") do |config|
    config.vm.provider "virtualbox" do |v|
     	v.memory = vmmemory
		v.cpus = numcpu
    end
    
    config.vm.define "manager" do |i|
		i.vm.box = "ubuntu/trusty64"
		i.vm.hostname = "manager"
		i.vm.network "private_network", ip: "#{manager_ip}"
		i.vm.provision "shell", path: "./provision.sh"
		if File.file?("./hosts") 
			i.vm.provision "file", source: "hosts", destination: "/tmp/hosts"
			i.vm.provision "shell", inline: "cat /tmp/hosts >> /etc/hosts", privileged: true
		end 
		i.vm.provision "shell", inline: "docker swarm init --advertise-addr #{manager_ip}; true"
		i.vm.provision "shell", inline: "docker swarm join-token -q worker > /vagrant/token; true"
		i.vm.provision "shell", inline: "mkdir mysql-datadir; true"
		config.vm.provision "file", source: "./docker-stack.yaml", destination: "docker-stack.yaml"
		config.vm.provision "file", source: "./monitoring-stack.yaml", destination: "monitoring-stack.yaml"
		config.vm.provision "file", source: "./app", destination: "app"
		config.vm.provision "file", source: "./nginx", destination: "nginx"
		config.vm.provision "file", source: "./php-fpm", destination: "php-fpm"
		
		config.vm.provision "docker" do |d|
			d.build_image "nginx", args: "-t nginxapp"
			d.build_image "php-fpm", args: "-t phpapp"
		end
	end 

	instances.each do |instance| 
		config.vm.define instance[:name] do |i|
			i.vm.box = "ubuntu/trusty64"
			i.vm.hostname = instance[:name]
			i.vm.network "private_network", ip: "#{instance[:ip]}"
			i.vm.provision "shell", path: "./provision.sh"
			if File.file?("./hosts") 
				i.vm.provision "file", source: "hosts", destination: "/tmp/hosts"
				i.vm.provision "shell", inline: "cat /tmp/hosts >> /etc/hosts", privileged: true
			end 
			
			i.vm.provision "shell", inline: "docker swarm join --advertise-addr #{instance[:ip]} --listen-addr #{instance[:ip]}:2377 --token `cat /vagrant/token` #{manager_ip}:2377; true"
		
			if (instance[:number] == "#{numworkers}")
				i.vm.provision :host_shell do |host_shell|
					host_shell.inline = 'vagrant ssh manager -- docker stack deploy --compose-file=docker-stack.yaml phpstack';
					host_shell.inline = 'vagrant ssh manager -- docker stack deploy --compose-file=monitoring-stack.yaml monitoring';
				end
			end
		end		
	end
end
