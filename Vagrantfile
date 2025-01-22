Vagrant.configure("2") do |config|
  # Máquina virtual con Debian
  config.vm.define "tomcat" do |tomcat|
    tomcat.vm.box = "debian/bullseye64"
    tomcat.vm.hostname = "tomcat.sistema.test"
    tomcat.vm.network "private_network", ip: "192.168.57.102"

    # Provisión de software en la máquina virtual
    tomcat.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update -y
      sudo apt-get upgrade -y

      # Instalación de OpenJDK 11
      sudo apt-get install -y openjdk-11-jdk

      # Instalación de Tomcat 9 y herramientas de administración
      sudo apt-get install -y tomcat9 tomcat9-admin

      # Configuración de usuarios de Tomcat
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

      # Instalación de Maven
      sudo apt-get install -y maven

      # Configuración del contexto para permitir acceso remoto
      sudo cp /vagrant/files/context.xml /usr/share/tomcat9-admin/host-manager/META-INF/context.xml
      sudo cp /vagrant/files/context.xml /usr/share/tomcat9-admin/manager/META-INF/context.xml

      # Reinicio de Tomcat
      sudo systemctl restart tomcat9

      # Verificación de versiones
      java -version
      mvn --version
      sudo systemctl status tomcat9
    SHELL

    # Sincronización de archivos entre la VM y el host
    tomcat.vm.synced_folder "./files", "/vagrant"
  end
end
