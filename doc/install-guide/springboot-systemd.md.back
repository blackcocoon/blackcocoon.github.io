---
sort: 2
---
# jar 서비스 등록 on cent OS

https://medium.com/@manjiki/running-spring-boot-applications-as-system-services-on-linux-5ea5f148c39a
https://www.auroria.io/spring-boot-as-systemd-service/
https://fabianlee.org/2018/04/15/java-spring-boot-application-as-a-service-using-systemd-on-ubuntu-16-04/
https://nalpari0628.tistory.com/entry/CentOS%EC%97%90-SpringBoot-jar%ED%8C%8C%EC%9D%BC-%EC%84%9C%EB%B9%84%EC%8A%A4-%EB%93%B1%EB%A1%9D%ED%95%98%EA%B8%B0
https://developer-ljo.tistory.com/26



## Create a systemd unit file

vim /etc/systemd/system/springboot-jar.service

```note
[Unit]  
Description=Spring boot Application Service - {service name}
After=network.target  
  
[Service]  
Type=forking  

User=springboot-user   
Group=springboot

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


## command

```sh
systemctl status springboot-jar

systemctl enable springboot-jar
systemctl status springboot-jar

systemctl start springboot-jar
systemctl status springboot-jar

systemctl stop springboot-jar
systemctl status springboot-jar
```