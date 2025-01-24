Vagrant.configure("2") do |config|
  # Máquina virtual TOMCAT
  config.vm.define "tomcat" do |tomcat|
    tomcat.vm.box = "debian/bullseye64"
    tomcat.vm.hostname = "tomcat.sistema.test"
    tomcat.vm.network "private_network", ip: "192.168.57.102"
    tomcat.vm.synced_folder "./files", "/home/vagrant/files"

    tomcat.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update -y
      sudo apt-get upgrade -y

      sudo apt-get install -y openjdk-11-jdk

      sudo apt-get install -y tomcat9 tomcat9-admin

      echo '
      <tomcat-users xmlns="http://tomcat.apache.org/xml"
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                    xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
                    version="1.0">
        <role rolename="manager-gui"/>
        <role rolename="admin-gui"/>
        <role rolename="manager-script"/>
        <user username="admin" password="password" roles="manager-gui,admin-gui,manager-script"/>
      </tomcat-users>
      ' | sudo tee /etc/tomcat9/tomcat-users.xml

      sudo apt-get install -y maven

      sudo cp /vagrant/files/context.xml /usr/share/tomcat9-admin/host-manager/META-INF/context.xml
      sudo cp /vagrant/files/context.xml /usr/share/tomcat9-admin/manager/META-INF/context.xml

      mkdir -p /home/vagrant/.m2

      sudo cp /home/vagrant/files/settings.xml /home/vagrant/.m2/settings.xml

      mkdir -p /home/vagrant/miwebapp

      sudo cp /home/vagrant/files/pom.xml /home/vagrant/miwebapp/pom.xml

      cd /home/vagrant
      if [ ! -d "rock-paper-scissors" ]; then
        git clone https://github.com/cameronmcnz/rock-paper-scissors.git
      fi
      cd rock-paper-scissors
      git checkout patch-1

      sudo cp /home/vagrant/files/pom.xml /home/vagrant/rock-paper-scissors/pom.xml

      mvn clean package
      mvn tomcat7:deploy

      sudo systemctl restart tomcat9

      java -version
      mvn --version
      sudo systemctl status tomcat9
    SHELL

    tomcat.vm.synced_folder "./files", "/vagrant"
  end
end
