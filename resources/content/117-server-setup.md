title=Server-Setup: jLAM-EE | Linux, Apache, MySQL and JavaEE 
subtitle=JavaEE Server Setup
created=2013-08-14T19:21:11
tags=CentOS, Linux, JavaEE, Java, MySQL, Glassfish, Setup, Installation
language=de

![CentOS, MySQL, Java & Glassfish: a perfekt team!](http://blog.matthias-reining.com/img/article-images/centos-mysql-java-glassfish.png "CentOS, MySQL, Java & Glassfish: a perfekt team!")  

Dieser Artikel ist eine Neuauflage des Artikels [Server-Setup: Linux, Java, MySQL und Glassfish](http://blog.matthias-reining.com/serversetup-linux-java-mysql-und-glassfish/). Beim Setup des Servers sind aber natürlich jede Menge neue Erfahrungen mit eingeflossen, so dass dieser Artikel noch besser ist als der 'Alte' ;-).

Die Anleitung beschreibt die Installation und das Setup folgender Komponenten:

* CentOS 6
* Apache HTTP Server
* MySQL
* Java (server version)
* Glassfish 4 (JavaEE 7 App Server)

Bei dem Artikel handelt es sich um 
ein kleines Tutorial, in dem die einzelnen (Linux) Befehle recht detailliert 
aufgeführt werden.

# Server

Die Installation findet auf einem virtuellen Server bei [server4you](http://server4you.de) statt. 

# CentOS

Server4you bietet die Möglichkeit fertige OS Templates zu verwenden. Hier wurde als Server Template **Cent OS 6 - minimal** gewählt.

Alternativ hätte man natürlich auch einen LAMP Stack auswählen können oder mit Hilfe von PLESK sehr komfortable und detailliert den Server konfigurieren können.

Ich habe mich für die *minimal* Variante entschieden mit der Hoffnung, dass somit nichts auf dem Server passiert, was ich nicht selbst eingerichtet habe.  
Dies ist zwar nicht die schnellste Variante und entspricht eher *totaler Kontrolle* oder *vertraue niemanden* ist aber aus meiner Sicht bei einem Server, der später als Application Server dienen soll, gar nicht so schlecht.


## Das Linux System

Als Linux Distribution kommt ein CentOS System zum Einsatz. Hierbei handelt es 
sich um einen RedHat Enterprise Linux (RHEL) Clone. Das System-Release lässt 
sich mit folgendem Befehl ermitteln:

    [root@intapprod ~]# cat /etc/*-release
	CentOS release 6.4 (Final)
	CentOS release 6.4 (Final)
	CentOS release 6.4 (Final)

Nachdem ich `vi` nicht so toll finde, habe ich als Texteditor noch `nano` installiert.

	[root@euve30387 glassfish]# yum install nano -y


# Apache HTTP Server

Der Apache HTTP Server wird per CentOS Paketmanager wie folgt installiert: 
	
	[root@intapprod ~]# yum install httpd
	Setting up Install Process
	Resolving Dependencies
	--> Running transaction check
	---> Package httpd.x86_64 0:2.2.15-29.el6.centos will be installed
	--> Processing Dependency: httpd-tools = 2.2.15-29.el6.centos for package: httpd-2.2.15-29.el6.centos.x86_64
	--> Processing Dependency: system-logos >= 7.92.1-1 for package: httpd-2.2.15-29.el6.centos.x86_64
	--> Processing Dependency: apr-util-ldap for package: httpd-2.2.15-29.el6.centos.x86_64
	--> Processing Dependency: libaprutil-1.so.0()(64bit) for package: httpd-2.2.15-29.el6.centos.x86_64
	--> Processing Dependency: libapr-1.so.0()(64bit) for package: httpd-2.2.15-29.el6.centos.x86_64
	--> Running transaction check
	---> Package apr.x86_64 0:1.3.9-5.el6_2 will be installed
	---> Package apr-util.x86_64 0:1.3.9-3.el6_0.1 will be installed
	---> Package apr-util-ldap.x86_64 0:1.3.9-3.el6_0.1 will be installed
	---> Package httpd-tools.x86_64 0:2.2.15-29.el6.centos will be installed
	---> Package redhat-logos.noarch 0:60.0.14-12.el6.centos will be installed
	--> Finished Dependency Resolution
	
	Dependencies Resolved
	
	==================================================================================
	 Package             Arch         Version                     Repository     Size
	==================================================================================
	Installing:
	 httpd               x86_64       2.2.15-29.el6.centos        updates       821 k
	Installing for dependencies:
	 apr                 x86_64       1.3.9-5.el6_2               base          123 k
	 apr-util            x86_64       1.3.9-3.el6_0.1             base           87 k
	 apr-util-ldap       x86_64       1.3.9-3.el6_0.1             base           15 k
	 httpd-tools         x86_64       2.2.15-29.el6.centos        updates        73 k
	 redhat-logos        noarch       60.0.14-12.el6.centos       base           15 M
	
	Transaction Summary
	==================================================================================
	Install       6 Package(s)
	
	Total download size: 16 M
	Installed size: 19 M
	Is this ok [y/N]: y
		
	Downloading Packages:
	(1/6): apr-1.3.9-5.el6_2.x86_64.rpm                                                                                    | 123 kB     00:00
	(2/6): apr-util-1.3.9-3.el6_0.1.x86_64.rpm                                                                             |  87 kB     00:00
	(3/6): apr-util-ldap-1.3.9-3.el6_0.1.x86_64.rpm                                                                        |  15 kB     00:00
	(4/6): httpd-2.2.15-29.el6.centos.x86_64.rpm                                                                           | 821 kB     00:00
	(5/6): httpd-tools-2.2.15-29.el6.centos.x86_64.rpm                                                                     |  73 kB     00:00
	(6/6): redhat-logos-60.0.14-12.el6.centos.noarch.rpm                                                                   |  15 MB     00:00
	----------------------------------------------------------------------------------------------------------------------------------------------
	Total                                                                                                          36 MB/s |  16 MB     00:00
	Running rpm_check_debug
	Running Transaction Test
	Transaction Test Succeeded
	Running Transaction
	  Installing : apr-1.3.9-5.el6_2.x86_64                                                                                                   1/6
	  Installing : apr-util-1.3.9-3.el6_0.1.x86_64                                                                                            2/6
	  Installing : httpd-tools-2.2.15-29.el6.centos.x86_64                                                                                    3/6
	  Installing : apr-util-ldap-1.3.9-3.el6_0.1.x86_64                                                                                       4/6
	  Installing : redhat-logos-60.0.14-12.el6.centos.noarch                                                                                  5/6
	  Installing : httpd-2.2.15-29.el6.centos.x86_64                                                                                          6/6
	  Verifying  : httpd-2.2.15-29.el6.centos.x86_64                                                                                          1/6
	  Verifying  : httpd-tools-2.2.15-29.el6.centos.x86_64                                                                                    2/6
	  Verifying  : apr-util-ldap-1.3.9-3.el6_0.1.x86_64                                                                                       3/6
	  Verifying  : apr-1.3.9-5.el6_2.x86_64                                                                                                   4/6
	  Verifying  : redhat-logos-60.0.14-12.el6.centos.noarch                                                                                  5/6
	  Verifying  : apr-util-1.3.9-3.el6_0.1.x86_64                                                                                            6/6
	
	Installed:
	  httpd.x86_64 0:2.2.15-29.el6.centos
	
	Dependency Installed:
	  apr.x86_64 0:1.3.9-5.el6_2                     apr-util.x86_64 0:1.3.9-3.el6_0.1                apr-util-ldap.x86_64 0:1.3.9-3.el6_0.1
	  httpd-tools.x86_64 0:2.2.15-29.el6.centos      redhat-logos.noarch 0:60.0.14-12.el6.centos
	
	Complete!
	[root@euve30387 ~]#

Start des Apache HTTP Servers:

	[root@euve30387 ~]# service httpd start
	Starting httpd:                                            [  OK  ]
	[root@euve30387 ~]#

Mit folgenden Befehl wird der Apache HTTP Server auch automatisch beim Server start mit gestartet:

	[root@euve30387 ~]# chkconfig --level 3 httpd on


# MySQL Datenbank

Die Datenbankinstallation orientiert sich an der Anleitung [http://dokuwiki.nausch.org/doku.php/centos:mysql](http://dokuwiki.nausch.org/doku.php/centos:mysql)

## Installation

Die Installation der MySQL Datenbank erfolgt ebenfalls mit dem Packagemanager *yum*.

	[root@euve30387 ~]# yum install mysql-server -y
	Setting up Install Process
	Resolving Dependencies
	--> Running transaction check
	---> Package mysql-server.x86_64 0:5.1.69-1.el6_4 will be installed
	--> Processing Dependency: mysql = 5.1.69-1.el6_4 for package: mysql-server-5.1.69-1.el6_4.x86_64
	--> Processing Dependency: perl-DBI for package: mysql-server-5.1.69-1.el6_4.x86_64
	--> Processing Dependency: perl-DBD-MySQL for package: mysql-server-5.1.69-1.el6_4.x86_64
	--> Processing Dependency: perl(DBI) for package: mysql-server-5.1.69-1.el6_4.x86_64
	--> Running transaction check
	---> Package mysql.x86_64 0:5.1.69-1.el6_4 will be installed
	---> Package perl-DBD-MySQL.x86_64 0:4.013-3.el6 will be installed
	---> Package perl-DBI.x86_64 0:1.609-4.el6 will be installed
	--> Finished Dependency Resolution
	
	Dependencies Resolved
	
	=============================================================================================
	 Package                  Arch             Version                   Repository         Size
	=============================================================================================
	Installing:
	 mysql-server             x86_64           5.1.69-1.el6_4            updates           8.7 M
	Installing for dependencies:
	 mysql                    x86_64           5.1.69-1.el6_4            updates           907 k
	 perl-DBD-MySQL           x86_64           4.013-3.el6               base              134 k
	 perl-DBI                 x86_64           1.609-4.el6               base              705 k
	
	Transaction Summary
	=============================================================================================
	Install       4 Package(s)
	
	Total download size: 10 M
	Installed size: 29 M
	Downloading Packages:
	(1/4): mysql-5.1.69-1.el6_4.x86_64.rpm                                | 907 kB     00:00
	(2/4): mysql-server-5.1.69-1.el6_4.x86_64.rpm                         | 8.7 MB     00:00
	(3/4): perl-DBD-MySQL-4.013-3.el6.x86_64.rpm                          | 134 kB     00:00
	(4/4): perl-DBI-1.609-4.el6.x86_64.rpm                                | 705 kB     00:00
	---------------------------------------------------------------------------------------------
	Total                                                         21 MB/s |  10 MB     00:00
	Running rpm_check_debug
	Running Transaction Test
	Transaction Test Succeeded
	Running Transaction
	  Installing : perl-DBI-1.609-4.el6.x86_64                                               1/4
	  Installing : perl-DBD-MySQL-4.013-3.el6.x86_64                                         2/4
	  Installing : mysql-5.1.69-1.el6_4.x86_64                                               3/4
	  Installing : mysql-server-5.1.69-1.el6_4.x86_64                                        4/4
	  Verifying  : perl-DBD-MySQL-4.013-3.el6.x86_64                                         1/4
	  Verifying  : mysql-server-5.1.69-1.el6_4.x86_64                                        2/4
	  Verifying  : mysql-5.1.69-1.el6_4.x86_64                                               3/4
	  Verifying  : perl-DBI-1.609-4.el6.x86_64                                               4/4
	
	Installed:
	  mysql-server.x86_64 0:5.1.69-1.el6_4
	
	Dependency Installed:
	  mysql.x86_64 0:5.1.69-1.el6_4              perl-DBD-MySQL.x86_64 0:4.013-3.el6
	  perl-DBI.x86_64 0:1.609-4.el6
	
	Complete!
	[root@euve30387 ~]



## Überprüfung der Konfiguration

	[root@euve30387 ~]# cat /etc/my.cnf
	[mysqld]
	datadir=/var/lib/mysql
	socket=/var/lib/mysql/mysql.sock
	user=mysql
	# Disabling symbolic-links is recommended to prevent assorted security risks
	symbolic-links=0
	
	[mysqld_safe]
	log-error=/var/log/mysqld.log
	pid-file=/var/run/mysqld/mysqld.pid
	[root@euve30387 ~]#


## DB Server starten

    [root@euve30387 ~]# service mysqld start
	Initializing MySQL database:  Installing MySQL system tables...
	OK
	Filling help tables...
	OK
	
	To start mysqld at boot time you have to copy
	support-files/mysql.server to the right place for your system
	
	PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !
	To do so, start the server, then issue the following commands:
	
	/usr/bin/mysqladmin -u root password 'new-password'
	/usr/bin/mysqladmin -u root -h euve30387.vserver.de password 'new-password'
	
	Alternatively you can run:
	/usr/bin/mysql_secure_installation
	
	which will also give you the option of removing the test
	databases and anonymous user created by default.  This is
	strongly recommended for production servers.
	
	See the manual for more instructions.
	
	You can start the MySQL daemon with:
	cd /usr ; /usr/bin/mysqld_safe &
	
	You can test the MySQL daemon with mysql-test-run.pl
	cd /usr/mysql-test ; perl mysql-test-run.pl
	
	Please report any problems with the /usr/bin/mysqlbug script!
	
	                                                           [  OK  ]
	Starting mysqld:                                           [  OK  ]
	[root@euve30387 ~]#


## Autostart der DB

    [root@euve30387 ~]# chkconfig mysqld on

## Server absichern

Nach der Installation ist die MySQL per default nicht gut abgesichert 
(Remote-Zugriff, Passwörter). Mit dem Script `mysql_secure_installation` kann
der Server abgesichert werden. Nach der Installation ist das `root` Passwort
leer, so dass die Frage nach dem "current password" einfach mit Enter bestätigt 
werden muss.
	
	[root@euve30387 ~]# /usr/bin/mysql_secure_installation
	
	
	NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MySQL
	      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!
	
	
	In order to log into MySQL to secure it, we'll need the current
	password for the root user.  If you've just installed MySQL, and
	you haven't set the root password yet, the password will be blank,
	so you should just press enter here.
	
	Enter current password for root (enter for none):
	OK, successfully used password, moving on...
	
	Setting the root password ensures that nobody can log into the MySQL
	root user without the proper authorisation.
	
	Set root password? [Y/n] y
	New password:
	Re-enter new password:
	Password updated successfully!
	Reloading privilege tables..
	 ... Success!
	
	
	By default, a MySQL installation has an anonymous user, allowing anyone
	to log into MySQL without having to have a user account created for
	them.  This is intended only for testing, and to make the installation
	go a bit smoother.  You should remove them before moving into a
	production environment.
	
	Remove anonymous users? [Y/n] y
	 ... Success!
	
	Normally, root should only be allowed to connect from 'localhost'.  This
	ensures that someone cannot guess at the root password from the network.
	
	Disallow root login remotely? [Y/n] y
	 ... Success!
	
	By default, MySQL comes with a database named 'test' that anyone can
	access.  This is also intended only for testing, and should be removed
	before moving into a production environment.
	
	Remove test database and access to it? [Y/n] y
	 - Dropping test database...
	 ... Success!
	 - Removing privileges on test database...
	 ... Success!
	
	Reloading the privilege tables will ensure that all changes made so far
	will take effect immediately.
	
	Reload privilege tables now? [Y/n] y
	 ... Success!
	
	Cleaning up...
	
	
	
	All done!  If you've completed all of the above steps, your MySQL
	installation should now be secure.
	
	Thanks for using MySQL!
	
	
	[root@euve30387 ~]#


# Java installieren

Um maximale Flexibilität zu haben, wird das JDK ohne Packagemanager installiert. Weiterhin hat man so die Möglichkeit das Server JRE zu installieren ([Oracle Java Download Seite](http://www.oracle.com/technetwork/java/javase/downloads/index.html)).

## Download Java

Der Download wird mittels `wget` durchgeführt (siehe [Download JDK via wget](http://blog.matthias-reining.com/download-jdk-via-wget/) )

	[root@euve30387 ~]# mkdir /data
	[root@euve30387 ~]# mkdir /data/jdk
	[root@euve30387 ~]# cd /data/jdk/

	[root@euve30387 jdk]# wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com" "http://download.oracle.com/otn-pub/java/jdk/7u25-b15/server-jre-7u25-linux-x64.tar.gz"

	--2013-08-14 16:00:17--  http://download.oracle.com/otn-pub/java/jdk/7u25-b15/server-jre-7u25-linux-x64.tar.gz
	Resolving download.oracle.com... 80.154.79.33, 80.154.79.65
	Connecting to download.oracle.com|80.154.79.33|:80... connected.
	HTTP request sent, awaiting response... 302 Moved Temporarily
	Location: https://edelivery.oracle.com/otn-pub/java/jdk/7u25-b15/server-jre-7u25-linux-x64.tar.gz [following]
	--2013-08-14 16:00:17--  https://edelivery.oracle.com/otn-pub/java/jdk/7u25-b15/server-jre-7u25-linux-x64.tar.gz
	Resolving edelivery.oracle.com... 23.45.102.140
	Connecting to edelivery.oracle.com|23.45.102.140|:443... connected.
	WARNING: certificate common name âwww.oracle.comâedelivery.oracle.comâ
	                                                                      HTTP request sent, awaiting response... 302 Moved Temporarily
	Location: http://download.oracle.com/otn-pub/java/jdk/7u25-b15/server-jre-7u25-linux-x64.tar.gz?AuthParam=1376496136_8cd1a291010ea256b9bf3c0dce10fbc0 [following]
	--2013-08-14 16:00:17--  http://download.oracle.com/otn-pub/java/jdk/7u25-b15/server-jre-7u25-linux-x64.tar.gz?AuthParam=1376496136_8cd1a291010ea256b9bf3c0dce10fbc0
	Reusing existing connection to download.oracle.com:80.
	HTTP request sent, awaiting response... 200 OK
	Length: 93316896 (89M) [application/x-gzip]
	Saving to: âserver-jre-7u25-linux-x64.tar.gz?AuthParam=1376496136_8cd1a291010ea256b9bf3c0dce10fbc0â
	
	100%[===================================================>] 93,316,896  17.5M/s   in 5.1s
	
	2013-08-14 16:00:22 (17.6 MB/s) - âserver-jre-7u25-linux-x64.tar.gz?AuthParam=1376496136_8cd1a291010ea256b9bf3c0dce10fbc0â
	
	[root@euve30387 jdk]#

## Entpacken und Installieren

Details hierzu können unter folgenden Link eingesehen werden: [http://docs.oracle.com/javase/7/docs/webnotes/install/linux/linux-server-jre.html](http://docs.oracle.com/javase/7/docs/webnotes/install/linux/linux-server-jre.html)

Umbennenen der herunterladenen Datei

	[root@euve30387 jdk]# mv server-jre-7u25-linux-x64.tar.gz\?AuthParam\=1376496136_8cd1a291010e


Die Installation orientiert sich an der Oracle Anleitung ([http://docs.oracle.com/javase/7/docs/webnotes/install/linux/linux-jdk.html](http://docs.oracle.com/javase/7/docs/webnotes/install/linux/linux-jdk.html))

	[root@euve30387 jdk]# tar zxvf server-jre-7u25-linux-x64.tar.gz

Nun wird noch ein Symlink erstellt, um später die JDK Version leicht upzudaten:

	[root@euve30387 jdk]# ln -s jdk1.7.0_25/ jdk-latest

Test

	[root@euve30387 jdk]# jdk-latest/bin/java -version
	java version "1.7.0_25"
	Java(TM) SE Runtime Environment (build 1.7.0_25-b15)
	Java HotSpot(TM) 64-Bit Server VM (build 23.25-b01, mixed mode)
	[root@euve30387 jdk]#


# Glassfish installieren

## Download und entpacken

Ich installiere hier einen promoted build des Glassfish Application Servers. Nachdem der GF4 noch recht neu ist, besteht dir möglichkeit durch nightly- oder promoted builds frühzeitig updates und bugfixes zu bekommen. Der promoted build wird allerdings als nicht-stable ausgewiesen!

	[root@euve30387 jdk]# cd /data/
	[root@euve30387 data]# mkdir glassfish
	[root@euve30387 data]# mkdir glassfish/gf-4.0.1-b01
	[root@euve30387 data]# cd glassfish/gf-4.0.1-b01/
	[root@euve30387 gf-4.0.1-b01]# wget http://dlc.sun.com.edgesuite.net/glassfish/4.0.1/promoted/glassfish-4.0.1-web-b01.zip

	[root@euve30387 gf-4.0.1-b01]# unzip glassfish-4.0.1-b01.zip

## Symlink 

	[root@euve30387 gf-4.0.1-b01]# cd ..
	[root@euve30387 glassfish]# ln -s gf-4.0.1-b01/ gf-prod
	[root@euve30387 glassfish]# ll
	total 4
	drwxr-xr-x 3 root root 4096 Aug 14 16:45 gf-4.0.1-b01
	lrwxrwxrwx 1 root root   13 Aug 14 16:47 gf-prod -> gf-4.0.1-b01/
	[root@euve30387 glassfish]#


## Startskript für Glassfish

Damit der Glassfish Server als Service gestartet werden kann, muss ein Startskript
angelegt werden.

	[root@euve30387 glassfish]# nano /etc/init.d/glassfish

    [root@intapprod data]# nano /etc/init.d/glassfish

    #!/bin/bash
	# description: Glassfish Start Stop Restart
	# processname: glassfish
	JAVA_HOME=/data/jdk/jdk-latest
	export JAVA_HOME
	PATH=$JAVA_HOME/bin:$PATH
	export PATH
	GLASSFISH_HOME=/data/glassfish/gf-prod/glassfish4
	
	case $1 in
	start)
	   sh $GLASSFISH_HOME/bin/asadmin start-domain domain1
	   ;;
	stop)
	   sh $GLASSFISH_HOME/bin/asadmin stop-domain domain1
	   ;;
	restart)
	   sh $GLASSFISH_HOME/bin/asadmin stop-domain domain1
	   sh $GLASSFISH_HOME/bin/asadmin start-domain domain1
	   ;;
	esac
	exit 0
	
### Skript Ausführbar machen

    [root@intapprod data]# chmod 755 /etc/init.d/glassfish
    [root@intapprod data]# chkconfig --add /etc/init.d/glassfish
    [root@intapprod data]# chkconfig --level 234 glassfish on

	[root@euve30387 glassfish]# chkconfig glassfish on

### Glassfish starten

	[root@euve30387 glassfish]# service glassfish start
	Waiting for domain1 to start ...........
	Successfully started the domain : domain1
	domain  Location: /data/glassfish/gf-4.0.1-b01/glassfish4/glassfish/domains/domain1
	Log File: /data/glassfish/gf-4.0.1-b01/glassfish4/glassfish/domains/domain1/logs/server.log
	Admin Port: 4848
	Command start-domain executed successfully.
	[root@euve30387 glassfish]#


### Admin Passwort ändern

Nach der Installation muss das Passwort geändert werden.
Greift man jedoch per Browser auf die Glassfish Admin Console 
([https://server:4848](http://server:4848)) bekommt man folgende Fehlermeldung:

> Secure Admin must be enabled to access the DAS remotely.

Hierzu muss der Befehl `enable-secure-admin` ausgeführt werden. 
Allerdings geht dies nicht, solange das *admin* Passwort leer ist. 
Das admin Passwort kann wie folgendermaßen geändert werden:

Zuerst Java Environment setzten:

	[root@euve30387 glassfish]# JAVA_HOME=/data/jdk/jdk-latest/
	[root@euve30387 glassfish]# export JAVA_HOME
	[root@euve30387 glassfish]# PATH=$JAVA_HOME/bin:$PATH

Glassfish Admin Console starten (das bestehende *password* ist 'leer', einfach Enter drücken).

    [root@euve30387 glassfish]# cd /data/glassfish/gf-prod/glassfish4/bin/
	[root@euve30387 bin]# ./asadmin

	Use "exit" to exit and "help" for online help.
	asadmin> change-admin-password \--user admin
	Enter the admin password>
	Enter the new admin password>
	Enter the new admin password again>
	Command change-admin-password executed successfully.
	asadmin> exit 


Nun kann der Befehl `enable-secure-admin` ausgeführt werden

    [root@euve30387 bin]# ./asadmin \--host localhost \--port 4848 enable-secure-admin
	Enter admin user name>  admin
	Enter admin password for user "admin">
	You must restart all running servers for the change in secure admin to take effect.
	Command enable-secure-admin executed successfully.
	[root@euve30387 bin]#

	[root@euve30387 bin]# service glassfish stop
	Waiting for the domain to stop ..
	Command stop-domain executed successfully.
	[root@euve30387 bin]# service glassfish start
	Waiting for domain1 to start .......................
	Successfully started the domain : domain1
	domain  Location: /data/glassfish/gf-4.0.1-b01/glassfish4/glassfish/domains/domain1
	Log File: /data/glassfish/gf-4.0.1-b01/glassfish4/glassfish/domains/domain1/logs/server.log
	Admin Port: 4848
	Command start-domain executed successfully.
	[root@euve30387 bin]#
	
	
Anschließend ist eine Anmeldung an der Admin Console [https://server:4848](https://server:4848) möglich.

### MySQL Treiber installieren

Hierzu muss der MySQL Treiber ins das Glassfish Module Verzeichnis kopiert werden. 
Den Treiber kann man sich per `wget`von [http://dev.mysql.com/downloads/connector/j/5.1.html](http://dev.mysql.com/downloads/connector/j/5.1.html) herunterladen.

	[root@euve30387 bin]# service glassfish stop
	[root@euve30387 data]# cd /data/
	[root@euve30387 data]# wget http://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.26.zip/from/http://cdn.mysql.com/
	[root@euve30387 data]# unzip mysql-connector-java-5.1.26.zip      

	[root@euve30387 data]# cp mysql-connector-java-5.1.26/mysql-connector-java-5.1.26-bin.jar /data/glassfish/gf-prod/glassfish4/glassfish/domains/domain1/lib/.

	[root@euve30387 bin]# service glassfish start



## My SQL User

	[root@euve30387 data]# mysql -u root -p
	Enter password:
	Welcome to the MySQL monitor.  Commands end with ; or \g.
	Your MySQL connection id is 3
	Server version: 5.1.69 Source distribution
	
	Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.
	
	Oracle is a registered trademark of Oracle Corporation and/or its
	affiliates. Other names may be trademarks of their respective
	owners.
	
	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
	
	mysql> CREATE USER 'conair'@'localhost' IDENTIFIED BY 'conair';
	Query OK, 0 rows affected (0.34 sec)
	
	mysql> GRANT ALL PRIVILEGES ON * . * TO 'conair'@'localhost';
	Query OK, 0 rows affected (0.00 sec)
	
	mysql> FLUSH PRIVILEGES;
	Query OK, 0 rows affected (0.00 sec)
	
	mysql> create database conair;
	Query OK, 1 row affected (0.00 sec)
	

## Glassfish Datasource

	[root@euve30387 bin]# ./asadmin create-jdbc-connection-pool --datasourceclassname=com.mysql.jdbc.jdbc2.optional.MysqlDataSource --restype=javax.sql.DataSource --property="user=conair:password=conair:url=jdbc\\:mysql\\://localhost\\:3306/conair" conairPool
	Enter admin user name>  admin
	Enter admin password for user "admin">
	JDBC connection pool conairPool created successfully.
	Command create-jdbc-connection-pool executed successfully.
	[root@euve30387 bin]# ./asadmin ping-connection-pool conairPool
	Enter admin user name>  admin
	Enter admin password for user "admin">
	Command ping-connection-pool executed successfully.

	[root@euve30387 bin]# ./asadmin create-jdbc-resource --connectionpoolid conairPool jdbc/conair
	Enter admin user name>  admin
	Enter admin password for user "admin">
	JDBC resource jdbc/conair created successfully.
	Command create-jdbc-resource executed successfully.
	[root@euve30387 bin]#


## Deployment

	[root@euve30387 bin]# ./asadmin deploy ~/deployments/conair.war
	Enter admin user name>  admin
	Enter admin password for user "admin">
	Application deployed with name conair.
	Command deploy executed successfully.
	[root@euve30387 bin]#


# Apache HTTP Server vor dem Glassfish Application Server

Nun steht unser System. Jetzt müssen die entsprechenden Domains noch in der Apache HTTP Server Konfiguration auf den Glassfish gemappt werden.

Ich würde hierzu die Verwendung von V-Host empfehlen. Mehr dazu gibt es dann vielleicht mal in einem weiteren Blog-Eintrag... :-)
