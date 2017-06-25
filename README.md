# JBoss-docker 

This is about all the commands that are required to run JBoss on docker and change JVM_OPTIONS. 

### Start docker #

	systemctl status docker.service

### pull JBoss or wildfly image

	docker pull jboss/wildfly

### To boot in standalone mode

	docker run -it jboss/wildfly

### To boot in domain mode
	
	docker run -it jboss/wildfly /opt/jboss/wildfly/bin/domain.sh -b 0.0.0.0 -bmanagement 0.0.0.0

### Change JVM options 

1. Option1: docker run command to start an interactive bash session 

```
[user@node2 ~]# docker run -it jboss/wildfly /bin/bash
[jboss@73759264c91f ~]$ ll
total 0
drwxrwxr-x. 10 jboss root 220 Aug 19  2016 wildfly
[jboss@73759264c9 ~]$ cd wildfly/
[jboss@73759264c9 wildfly]$ ll
total 400
-rw-rw-r--. 1 jboss root  26530 Aug 19  2016 LICENSE.txt
-rw-rw-r--. 1 jboss root   2510 Aug 19  2016 README.txt
drwxrwxr-x. 3 jboss root     27 Aug 19  2016 appclient
drwxrwxr-x. 3 jboss root   4096 Jun  5 17:53 bin
-rw-rw-r--. 1 jboss root   2451 Aug 19  2016 copyright.txt
drwxrwxr-x. 6 jboss root     67 Aug 19  2016 docs
drwxrwxr-x. 5 jboss root     50 Aug 19  2016 domain
-rw-rw-r--. 1 jboss root 365666 Aug 19  2016 jboss-modules.jar
drwxrwxr-x. 3 jboss root     20 Aug 19  2016 modules
drwxrwxr-x. 6 jboss root     68 Aug 19  2016 standalone
drwxrwxr-x. 2 jboss root    242 Jun  5 17:53 welcome-content
[jboss@73759264c9 wildfly]$ cd bin/
[jboss@73759264c9 bin]$ vi standalone.conf
[jboss@73759264c9 bin]$ nohup ./standalone.sh &
[1] 20
[jboss@73759264c9 bin]$ nohup: ignoring input and appending output to 'nohup.out'
[jboss@73759264c9 bin]$ ps -ef 
UID         PID   PPID  C STIME TTY          TIME CMD
jboss         1      0  0 11:45 ?        00:00:00 /bin/bash
jboss        20      1  0 11:46 ?        00:00:00 /bin/sh ./standalone.sh
jboss        65     20  0 11:46 ?        00:00:30 /usr/lib/jvm/java/bin/java -D[Standalone] -server -Xms512m -Xmx512m -XX:MetaspaceSize.....
jboss       174      1  0 14:10 ?        00:00:00 ps -ef
```

From the other terminal, check docker process: 

```
[root@node2 ~]# ps -ef 
--
--
root      29886      2  0 17:15 ?        00:00:04 [xfsaild/dm-9]
root      29890   8781  0 17:15 ?        00:00:00 /usr/bin/docker-containerd-shim-current 73759264c91fff410c1e417c7dad427a5ffb5e3bc5ba5
1000      29905  29890  0 17:15 pts/1    00:00:00 /bin/bash
1000      30050  29905  0 17:16 pts/1    00:00:00 /bin/sh ./standalone.sh
1000      30095  30050  0 17:16 pts/1    00:00:31 /usr/lib/jvm/java/bin/java -D[Standalone] -server -Xms512m -Xmx512m -XX:MetaspaceSize
root      34914      2  0 Jun24 ?        00:00:00 [kworker/0:2H]
postfix   41092   1812  0 19:10 ?        00:00:00 pickup -l -t unix -u
--
--
```

pts1 for bin/bash; jboss process is the child process to my /bin/bash

2. Option2: Start Jboss container like 

	docker run -it --env JAVA_OPTS="-server -Xms512m -Xmx512m" jboss/wildfly

3. Option3 :  Extend the standard image by creating a Dockerfile containing; build and run the container:

	FROM jboss/wildfly:latest
	COPY standalone.conf $JBOSS_HOME/bin/



    docker build --tag=wildfly-test .
    docker run -it wildfly-app


