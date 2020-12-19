---
sort: 1
---

# Install Tomcat 9 on CentOS 7

https://linuxize.com/post/how-to-install-tomcat-9-on-centos-7/

This tutorial covers the steps required to install Tomcat 9.0 on CentOS 7.

## Prerequisites

### Install OpenJDK
Tomcat 9 requires Java SE 8 or later. Install Java by typing the following command:
```sh
sudo yum list java*
sudo yum install java-1.8.0-openjdk-devel
```

### Create Tomcat system user
Running Tomcat as the root user is a security risk and not considered best practice.  
We’ll create a new system user and group with home directory /opt/tomcat that will run the Tomcat service:
```sh
sudo useradd -m -U -d /opt/tomcat -s /bin/false tomcat
```

## Download Tomcat

Navigate to the /tmp directory and download the Tomcat zip file using the following wget command :
```sh
cd /tmp
wget https://downloads.apache.org/tomcat/tomcat-9/v9.0.41/bin/apache-tomcat-9.0.41.tar.gz
```

When the download is complete, extract the tar file :
```sh
tar -xf apache-tomcat-9.0.41.tar.gz
```

Move the Tomcat source files to it to the /opt/tomcat directory:
```sh
sudo mv apache-tomcat-9.0.41 /opt/tomcat/
```

Tomcat 9 is updated frequently.  
To have more control over versions and updates, we’ll create a symbolic link called latest, that points to the Tomcat installation directory:
```sh
sudo ln -s /opt/tomcat/apache-tomcat-9.0.41 /opt/tomcat/latest
```

The tomcat user that we previously set up needs to have access to the tomcat installation directory.  
Run the following command to change the directory ownership to user and group tomcat:
```sh
sudo chown -R tomcat: /opt/tomcat
```

Make the scripts inside the bin directory executable by issuing the following `chmod` command:
```sh
sudo sh -c 'chmod +x /opt/tomcat/latest/bin/*.sh'
```

## Create a systemd unit file
To make Tomcat run as a service open your text editor and create a tomcat.service unit file in the /etc/systemd/system/ directory:

```sh
sudo vim /etc/systemd/system/tomcat.service
```

Paste the following content:

```note
[Unit]  
Description=Tomcat 9 servlet container  
After=network.target  
  
[Service]  
Type=forking  

User=tomcat   
Group=tomcat  

Environment="JAVA_HOME=/usr/lib/jvm/jre"   
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom"  

Environment="CATALINA_BASE=/opt/tomcat/latest"  
Environment="CATALINA_HOME=/opt/tomcat/latest"  
Environment="CATALINA_PID=/opt/tomcat/latest/temp/tomcat.pid"  
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"  

ExecStart=/opt/tomcat/latest/bin/startup.sh  
ExecStop=/opt/tomcat/latest/bin/shutdown.sh  

[Install]  
WantedBy=multi-user.target  

```
Save and close the file.

Notify systemd that we created a new unit file by typing:

```sh
sudo systemctl daemon-reload
```

Enable and start the Tomcat service:

```sh
sudo systemctl enable tomcat
sudo systemctl start tomcat
```

Check the service status with the following command:

```sh
sudo systemctl status tomcat
```

```sh
● tomcat.service - Tomcat 9 servlet container
   Loaded: loaded (/etc/systemd/system/tomcat.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2018-11-15 20:47:50 UTC; 4s ago
  Process: 1759 ExecStart=/opt/tomcat/latest/bin/startup.sh (code=exited, status=0/SUCCESS)
 Main PID: 1767 (java)
   CGroup: /system.slice/tomcat.service
```

## Adjust the Firewall
If your server is protected by a firewall and you want to access the tomcat interface from the outside of the local network, you need to open port 8080.

Use the following commands to open the necessary port:

```sh
sudo firewall-cmd --zone=public --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
```

In most cases, when running Tomcat in a production environment, you will use a load balancer or reverse proxy. It’s a best practice to allow access to port 8080 only to your internal network.


## Configure Tomcat Web Management Interface
At this point Tomcat is installed, and we can access it with a web browser on port 8080, but we can not access the web management interface because we have not created a user yet.

Tomcat users and their roles are defined in the tomcat-users.xml file.

If you open the file, you will notice that it is filled with comments and examples describing how to configure the file.

```sh
sudo vim /opt/tomcat/latest/conf/tomcat-users.xml
```

To add a new user that will be able to access the tomcat web interface (manager-gui and admin-gui) you need to define the user in tomcat-users.xml file as shown below. Make sure you change the username and password to something more secure:

/opt/tomcat/latest/conf/tomcat-users.xml
```xml
<tomcat-users>
<!--
    Comments
-->
   <role rolename="admin-gui"/>
   <role rolename="manager-gui"/>
   <user username="admin" password="admin_password" roles="admin-gui,manager-gui"/>
</tomcat-users>
```
By default Tomcat web management interface is configured to allow access only from the localhost. If you want to be able to access the web interface from a remote IP or from anywhere which is not recommended because it is a security risk you can open the following files and make the following changes.

If you need to access the web interface from anywhere open the following files and comment or remove the lines highlighted in yellow:

/opt/tomcat/latest/webapps/manager/META-INF/context.xml
```xml
<Context antiResourceLocking="false" privileged="true" >
<!--
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
-->
</Context>
```

/opt/tomcat/latest/webapps/host-manager/META-INF/context.xml
```xml
<Context antiResourceLocking="false" privileged="true" >
<!--
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
-->
</Context>
```

If you need to access the web interface only from a specific IP, instead of commenting the blocks add your public IP to the list. Let’s say your public IP is 41.41.41.41 and you want to allow access only from that IP:

/opt/tomcat/latest/webapps/manager/META-INF/context.xml
```xml
<Context antiResourceLocking="false" privileged="true" >
  <Valve className="org.apache.catalina.valves.RemoteAddrValve" 
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1|41.41.41.41" />
</Context>
```

/opt/tomcat/latest/webapps/host-manager/META-INF/context.xml
```xml
<Context antiResourceLocking="false" privileged="true" >
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1|41.41.41.41" />
</Context>
```

The list of allowed IP addresses is a list separated with vertical bar |. You can add single IP addresses or use a regular expressions.

Once done, restart the Tomcat service for changes to take effect:

```sh
sudo systemctl restart tomcat
```

### Test the Installation
Open your browser and type: `http://<your_domain_or_IP_address>:8080`

Upon successful installation, a screen similar to the following should appear:

![Tomcat 9](/assets/images/install-guide/tomcat-1.jpg)


Tomcat web application manager dashboard is available at `http://<your_domain_or_IP_address>:8080/manager/html`. From here, you can deploy, undeploy, start, stop, and reload your applications.

![Tomcat web application manager](/assets/images/install-guide/tomcat-2.jpg)



Tomcat virtual host manager dashboard is available at `http://<your_domain_or_IP_address>:8080/host-manager/html`. From here, you can create, delete, and manage Tomcat virtual hosts.

![Tomcat virtual host manager](/assets/images/install-guide/tomcat-3.jpg)


## Conclusion
You have successfully installed Tomcat 9.0 on your CentOS 7 system and learned how to access the Tomcat management interface. You can now visit the official Apache Tomcat 9.0 Documentation and learn more about the Apache Tomcat features.

If you hit a problem or have feedback, leave a comment below.