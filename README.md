# Java remote debug and monitor
1.Remote debug Java with Eclipse
Add the following to Java applications in the remote machine:

java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y  suspend=y -jar <application.jar>

In the machine run eclipse
ssh -L 127.0.0.1:8001:127.0.0.1:8001 -N <user@remote machine>

In eclipse “Debug Configurations”, add “Remote Java Application” 
Connection Type: Standard (Socket Attach)
Host: localhost
Port: 8001

2.Monitor remote Java application
Add the following to Java application in the remote machine:
java  -Djava.rmi.server.hostname=<remote machine> -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=9998 -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false -jar <application.jar>

netstat -an | grep 9998

In the local machine:
ssh -v -D 9998 <user@remote machine>

jconsole:
jconsole -J-DsocksProxyHost=localhost -J-DsocksProxyPort=9998 service:jmx:rmi:///jndi/rmi://<remote machine>:9998/jmxrmi

jvisualvm :
jvisualvm -J-DsocksProxyHost=localhost -J-DsocksProxyPort=9998

Add JMX with <remote machine>:9998

jstatd: 
The advantage is the application running as it is. jstatd can start and stop at any time. The disadvantage is less information. 
In the remote machine:
bash> cat jstatd.all.policy
grant codebase 'file:${java.home}/../lib/tools.jar' {
	permission java.security.AllPermission;
}
bash> sudo /path/to/JDK/bin/jstatd -J-Djava.security.policy=jstatd.all.policy
 -p 1099
Note: You can specify port with -p number and get more info with -J-Djava.rmi.server.logCalls=true

In localhost:
ssh -v -D 9698 <user@remote machine>
jvisualvm -J-DsocksProxyHost=localhost -J-DsocksProxyPort=9998
Add jstatd with port 1099

