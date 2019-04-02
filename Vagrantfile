# See: https://manski.net/2016/09/vagrant-multi-machine-tutorial/
# for information about machine names on private network
Vagrant.configure("2") do |config|
  config.vm.box = "envimation/ubuntu-xenial"

  config.vm.provision "shell", inline: "echo Hello"

  config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update -y
    sudo apt-get install iputils-ping -y
    sudo apt-get install -y avahi-daemon libnss-mdns
    ifconfig
  SHELL

  config.vm.define "web" do |web|
    web.vm.box = "envimation/ubuntu-xenial"
    web.vm.hostname = "web"
    web.vm.network "private_network", ip: "192.168.33.10"

    web.vm.provider "virtualbox" do |v|
      # We need 2048Mb so that Wildfly and Errai can execute
      v.memory = 2048
    end

    # We want to access wildfly from the host using port 8080
    web.vm.network "forwarded_port", guest: 8080, host: 8080

    web.vm.provision "shell", inline: <<-SHELL
      sudo apt-get install openjdk-8-jdk-headless -y
      sudo apt-get install git -y
      wget http://download.jboss.org/wildfly/15.0.1.Final/wildfly-15.0.1.Final.tar.gz
      tar -xvzf wildfly-15.0.1.Final.tar.gz
      # So that wildlfy accepts remote connections
      sed -i 's#<inet-address value="${jboss\.bind\.address:127\.0\.0\.1}"/>#<any-address/>#g' /home/vagrant/wildfly-15.0.1.Final/standalone/configuration/standalone.xml
    SHELL

    web.vm.provision "shell", :run => 'always', inline: <<-SHELL
      # We assume that errai-demonstration-gradle.war is located in the host folder that contains the Vagrantfile
      sudo cp /vagrant/errai-demonstration-gradle.war /home/vagrant/wildfly-15.0.1.Final/standalone/deployments/errai-demonstration-gradle.war
      # So that the wildfly server always starts
      sudo nohup sh /home/vagrant/wildfly-15.0.1.Final/bin/standalone.sh > /dev/null &
    SHELL
  end

  config.vm.define "db" do |db|
    db.vm.box = "envimation/ubuntu-xenial"
    db.vm.hostname = "db"
    db.vm.network "private_network", ip: "192.168.33.11"

    # We want to access H2 console from the host using port 8082
    # We want to connet to the H2 server using port 9092
    db.vm.network "forwarded_port", guest: 8082, host: 8082
    db.vm.network "forwarded_port", guest: 9092, host: 9092

    db.vm.provision "shell", inline: <<-SHELL
      sudo apt-get install openjdk-8-jdk-headless -y
      sudo apt-get install unzip -y
      wget http://repo2.maven.org/maven2/com/h2database/h2/1.4.199/h2-1.4.199.jar
    SHELL

    # To connect to H2 use: jdbc:h2:tcp://192.168.33.11:9092/~/test
    # How to setup java as a service in ubuntu: http://www.jcgonzalez.com/ubuntu-16-java-service-wrapper-example
    db.vm.provision "shell", :run => 'always', inline: <<-SHELL
      # So that the H2 server always starts
      java -cp ./h2*.jar org.h2.tools.Server -web -webAllowOthers -tcp -tcpAllowOthers -ifNotExists > ~/out.txt &
    SHELL

  end
end
