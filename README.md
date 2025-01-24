# Tomcat-y-Maven-Aplicaciones-Java

# **Instalación y Configuración de Tomcat y Maven con Vagrant**

Este documento describe los pasos para configurar un entorno con **Tomcat 9 y Maven** en **Vagrant** con **Debian**, siguiendo el orden del documento original con las soluciones implementadas.


## **1. Introducción**

En esta práctica realizaremos un despliegue de una aplicación Java usando Tomcat y Maven. Se utilizará **Tomcat 9** porque soporta **Java 8 y superiores**, siendo compatible con muchos proyectos reales.

## **2. Instalación de Tomcat**

### **Instalación de OpenJDK**

```bash
sudo apt install -y openjdk-11-jdk
```

### **Instalación de Tomcat**

```bash
sudo apt install -y tomcat9 tomcat9-admin
```

Verificar que el servicio está activo:

```bash
sudo systemctl status tomcat9
```

![Imagen tomcat](img/Captura.PNG)

## **3. Configuración de Administración**

### **Configuración de Usuarios en Tomcat**
Editar `/etc/tomcat9/tomcat-users.xml` y añadir:

```xml
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
  <role rolename="manager-gui"/>
  <role rolename="admin-gui"/>
  <role rolename="manager-script"/>
  <user username="admin" password="password" roles="manager-gui,admin-gui,manager-script"/>
</tomcat-users>
```

Reiniciar Tomcat:

```bash
sudo systemctl restart tomcat9
```

## **4. Despliegue Manual mediante GUI**
1. Acceder a **http://192.168.57.102:8080/manager/html** con `admin/password`.

![Imagen tomcat](img/Captura2.PNG)

2. Buscar la opción para desplegar WAR.
3. Seleccionar `tomcat1.war` y hacer clic en **Deploy**.

![Imagen tomcat](img/Captura4.PNG)

## **5. Despliegue con Maven**

### **Instalación de Maven**

```bash
sudo apt-get update && sudo apt-get -y install maven
```

![Imagen tomcat](img/Captura3.PNG)


### **Configuración de Maven (`pom.xml`)**

```xml
<build>
  <finalName>miwebapp</finalName>
  <plugins>
    <plugin>
      <groupId>org.apache.tomcat.maven</groupId>
      <artifactId>tomcat7-maven-plugin</artifactId>
      <version>2.2</version>
      <configuration>
        <url>http://192.168.57.102:8080/manager/text</url>
        <server>Tomcat</server>
        <path>/miwebapp</path>
      </configuration>
    </plugin>
  </plugins>
</build>
```

### **Configuración de Credenciales de Maven (`settings.xml`)**
Ubicar `/etc/maven/settings.xml` y añadir:

```xml
<settings>
  <servers>
    <server>
      <id>Tomcat</id>
      <username>admin</username>
      <password>password</password>
    </server>
  </servers>
</settings>
```

### **Desplegar la Aplicación con Maven**

```bash
mvn clean package
mvn tomcat7:deploy
```

Para actualizar sin eliminar:

```bash
mvn tomcat7:redeploy
```

Para eliminar la aplicación:

```bash
mvn tomcat7:undeploy
```

## **6. Copiar Archivos Fuera de la VM**

Usando `vagrant scp`:

```bash
vagrant scp tomcat:/home/vagrant/miwebapp/pom.xml ./files/pom.xml
vagrant scp tomcat:/home/vagrant/.m2/settings.xml ./files/settings.xml
```

## **7. Verificación Final**
Verificar el estado del servicio:
```bash
sudo systemctl status tomcat9
```
Acceder a la aplicación en:

http://192.168.57.102:8080/miwebapp


![Imagen tomcat](img/Captura5.PNG)