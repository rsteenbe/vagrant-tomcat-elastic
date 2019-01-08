# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: "echo Provision 2 ubuntu/xenial64 boxes"
  
  config.vm.define "tomcat" do |server2|
    server2.vm.box = "ubuntu/xenial64"
	server2.vm.network 'forwarded_port', guest: 80, host: 80
	server2.vm.network 'forwarded_port', guest: 1099, host: 1099
	server2.vm.network 'forwarded_port', guest: 1100, host: 1100
	server2.vm.network 'forwarded_port', guest: 8080, host: 8080
	server2.vm.network "private_network", ip: "192.168.50.11"
	server2.vm.provider "virtualbox" do |v|
		v.memory = 4096
	end
	server2.vm.provision "shell", inline: <<-SHELL
	
	# CONFIGURE VERSIONS BELOW
	export TOMCAT_VERSION=9.0.14
	export JAVA_VERSION=8
  
	# Update machine
	apt-get update

	# Install Java 8
	apt-get install openjdk-${JAVA_VERSION}-jdk -y

	# Install & Configure Tomcat 9
	sudo groupadd tomcat
	cd /tmp
	wget -q "http://apache.mirror.ipcheck.nu/tomcat/tomcat-9/v${TOMCAT_VERSION}/bin/apache-tomcat-${TOMCAT_VERSION}.tar.gz"
	sudo mkdir /opt/tomcat
	sudo tar xzvf /tmp/apache-tomcat-${TOMCAT_VERSION}.tar.gz -C /opt/tomcat --strip-components=1
	cd /opt/tomcat
	sudo chmod -R 777 /opt/tomcat
	sudo cp /vagrant/tomcat.service /etc/systemd/system/tomcat.service
	sudo systemctl daemon-reload
	sudo systemctl start tomcat
	sudo systemctl status tomcat
	sudo systemctl enable tomcat
	sed -i 's|</tomcat-users>|<user username="admin" password="password" roles="manager-gui,admin-gui"/></tomcat-users>|' /opt/tomcat/conf/tomcat-users.xml
	sed -i 's|<Context antiResourceLocking="false" privileged="true" >|<Context antiResourceLocking="false" privileged="true" ><!--|' /opt/tomcat/webapps/manager/META-INF/context.xml
	sed -i 's|</Context>|--></Context>|' /opt/tomcat/webapps/manager/META-INF/context.xml
	sed -i 's|<Context antiResourceLocking="false" privileged="true" >|<Context antiResourceLocking="false" privileged="true" ><!--|' /opt/tomcat/webapps/host-manager/META-INF/context.xml
	sed -i 's|</Context>|--></Context>|' /opt/tomcat/webapps/host-manager/META-INF/context.xml
	sudo systemctl restart tomcat	
	
	SHELL
  end
  
  config.vm.define "elastic" do |server1|
    server1.vm.box = "ubuntu/xenial64"
	server1.vm.network 'forwarded_port', guest: 9200, host: 9200
	server1.vm.network 'forwarded_port', guest: 9300, host: 9300
	server1.vm.network "private_network", ip: "192.168.50.10"
	server1.vm.provider "virtualbox" do |v|
		v.memory = 4096
	end
	server1.vm.provision "shell", inline: <<-SHELL
	
	# Update
	apt-get update
	
	# Install Java 8
	apt-get install openjdk-8-jdk -y
	
	# Install & Configure Elastic Search 6
	wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
	apt-get install apt-transport-https
	echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
	apt-get update && sudo apt-get install elasticsearch
	echo Config Elastic Search
	sed -i 's/#network.host: 192.168.0.1/network.host: 0.0.0.0/' /etc/elasticsearch/elasticsearch.yml
	sed -i 's/#cluster.name: my-application/cluster.name: renclastic-cluster/' /etc/elasticsearch/elasticsearch.yml
	sed -i 's/#node.name: node-1/node.name: renclastic-node/' /etc/elasticsearch/elasticsearch.yml
	sysctl -w vm.max_map_count=262144
	/bin/systemctl daemon-reload
	/bin/systemctl enable elasticsearch.service
	systemctl start elasticsearch.service
	chmod -R 777 /var/log/elasticsearch
	chmod -R 777 /var/lib/elasticsearch
	chmod -R 777 /usr/share/elasticsearch
	chmod -R 777 /etc/elasticsearch
	chmod -R 777 /etc/default/elasticsearch
	
	SHELL
  end 
end