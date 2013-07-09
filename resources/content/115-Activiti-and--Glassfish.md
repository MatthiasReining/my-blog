title=Installing Activiti on Glassfish
subtitle=works perfect...
created=2013-07-09T23:43:11
tags=Java, Glassfish, Activiti, Installing Activiti on Glassfish, Activiti 5.13, Glassfish 4.0.1
language=en

* Download Glassfish application server (in my case promoted build [http://dlc.sun.com.edgesuite.net/glassfish/4.0.1/promoted/](http://dlc.sun.com.edgesuite.net/glassfish/4.0.1/promoted/ "http://dlc.sun.com.edgesuite.net/glassfish/4.0.1/promoted/")).

* Unzip glassfish-4.0.1-b01.zip and start server `glassfish4\bin\asadmin start-domain`

* Download Activiti (here [http://www.activiti.org/download.html](http://www.activiti.org/download.html "activiti-5.13.zip"))

* Unzip activiti-5.13.zip

* Deploy Activiti war-files to Glassfish by using the admin ui ([http://localhost:4848](http://localhost:4848 "http://localhost:4848"))
	* using `activiti-5.13.zip\war\activiti-rest.war` and  `activiti-5.13.zip\war\activiti-rest.war`  

* Start Acitiviti Explorer ([localhost:8080/activiti-explorer](localhost:8080/activiti-explorer "localhost:8080/activiti-explorer"))

The deployment of Activiti 5.13 on Glassfish v. 4.0.1 works perfect out of the box!
