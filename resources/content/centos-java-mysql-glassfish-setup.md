title=Server-Setup: Linux, Java, MySQL und Glassfish
subtitle=Eine Glassfish Installationsanleitung
created=2013-02-08T03:13:12
tags=Java, MySQL, Glassfish, Setup

![CentOS, MySQL, Java & Glassfish: a perfekt team!](/img/article-images/centos-mysql-java-glassfish.png "CentOS, MySQL, Java & Glassfish: a perfekt team!")  
Der Artikel beschreibt die Installation eines Glassfish Application Servers
mit einer MySQL Datenbank auf einem Linux (CentOS) System. Die Beschreibung
startet mit einem *blankem* CentOS System. sBei dem Artikel handelt es sich um 
ein kleines Tutorial in dem die einzelnen (Linux) Befehle recht detailliert 
aufgeführt werden.

## Das Linux System

Bei dem System handelt es sich um ein CentOS System. Hierbei handelt es sich um 
einen RedHat Enterprise Linux (RHEL) clone. Das System release lässt sich mit 
folgendem Befehl ermitteln:

    [root@intapprod ~]# cat /etc/*-release
    CentOS release 6.3 (Final)
    CentOS release 6.3 (Final)
    CentOS release 6.3 (Final)

## Installation der MySQL Datenbank

Die Datenbankinstallation orientiert sich an der Anleitung [http://dokuwiki.nausch.org/doku.php/centos:mysql](http://dokuwiki.nausch.org/doku.php/centos:mysql)

### Installation

Die Installation der MySQL Datenbank erfolgt mit dem Packagemanager *yum*.

    [root@intapprod ~]# yum install mysql-server -y

### Überprüfung der Konfiguration

    [root@intapprod ~]# cat /etc/my.cnf
    [mysqld]
    datadir=/var/lib/mysql
    socket=/var/lib/mysql/mysql.sock
    user=mysql
    # Disabling symbolic-links is recommended to prevent assorted security risks
    symbolic-links=0

    [mysqld_safe]
    log-error=/var/log/mysqld.log
    pid-file=/var/run/mysqld/mysqld.pid

### DB Server starten

    [root@intapprod ~]# service mysqld start

### Autostart der DB

    [root@intapprod ~]# chkconfig mysqld on

### Server absichern

Nach der Installation ist die MySQL per default nicht gut abgesichert 
(Remote-Zugriff, Passwörter). Mit dem Script `mysql_secure_installation` kann
der Server abgesichert werden. Nach der Installation ist das `root` Passwort
leer, so dass die Frage danach einfach mit Enter bestätigt werden muss.

    [root@intapprod ~]# /usr/bin/mysql_secure_installation
    ...
    Enter current password for root (enter for none):  <Enter>
    ...
    Set root password? [Y/n] <y>
    ...
    New password: <Password>
    Re-enter new password: <Password>
    ...
    Remove anonymous users? [Y/n] <y>
    ...
    Disallow root login remotely? [Y/n] <y>
    ...
    Remove test database and access to it? [Y/n] <y>
    ...
    Reload privilege tables now? [Y/n] <y>

### Datenbank Schematas und Backup einspielen

Bei Bedarf kann mit folgedem Befehl ein Backup-Script (SQL Dump) eingespielt 
werden.

    [root@intapprod ~]# mysql -u root -p < backup.sql

Der Importvorgang zeigt nichts auf der Konsole an.  
Mit folgendem Befehl kann man einen Überblick über die vorhanden Datenbanken 
in der MySQL bekommen, so dass man überprüfen kann, ob ein neue Datenbank 
angelegt worden ist.

    [root@intapprod ~]# mysql -u root -p
    ...
    mysql> show databases;
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | my-database        |
    | mysql              |
    +--------------------+
    3 rows in set (0.00 sec)


## Java installieren

Um maximale Flexibilität zu haben, wird das JDK ohne Packagemanager installiert.
Die Installation orientiert sich an der Oracle Anleitung ([http://docs.oracle.com/javase/7/docs/webnotes/install/linux/linux-jdk.html](http://docs.oracle.com/javase/7/docs/webnotes/install/linux/linux-jdk.html))

Nachdem für den JDK Download auf der Oracle Webseite die Lizenzbedingungen 
bestätigt werden müssen, ist ein einfacher Download per `wget` nicht möglich. 
Das JDK kann aber über einen Browser heruntergeladen werden ([http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)) und anschileßend per (Win)SCP auf den Server kopiert werden.  
Mit folgenden Befehlen wird das JDK unter `/data` installiert.

    [root@intapprod ~]# cd /
    [root@intapprod /]# mkdir data
    [root@intapprod /]# cd data/
    [root@intapprod data]# mkdir java
    [root@intapprod data]# cd java/
    [root@intapprod java]# mv ~/jdk-7u13-linux-x64.gz .
    [root@intapprod java]# tar zxvf jdk-7u13-linux-x64.gz
    [root@intapprod java]# jdk1.7.0_13/bin/java -version
    java version "1.7.0_13"
    Java(TM) SE Runtime Environment (build 1.7.0_13-b20)
    Java HotSpot(TM) 64-Bit Server VM (build 23.7-b01, mixed mode)

## Glassfish installieren

Als Appserver wird Oracle Glassfish Open Source Edition eingesetzt. 
Die Installation orientiert sich (sehr grob) an [http://www.davidghedini.com/pg/entry/install_glassfish_3_1_on](http://www.davidghedini.com/pg/entry/install_glassfish_3_1_on).

    [root@intapprod data]# wget http://download.java.net/glassfish/3.1.2.2/release/glassfish-3.1.2.2.zip
    [root@intapprod data]# unzip glassfish-3.1.2.2.zip

### Startskript für Glassfish

Damit der Glassfish Server als Service gestartet werden kann, muss ein Startskript
angelegt werden.

    [root@intapprod data]# nano /etc/init.d/glassfish

    #!/bin/bash
    # description: Glassfish Start Stop Restart
    # processname: glassfish
    # chkconfig: 234 20 80
    JAVA_HOME=/data/java/jdk1.7.0_13
    export JAVA_HOME
    PATH=$JAVA_HOME/bin:$PATH
    export PATH
    GLASSFISH_HOME=/data/glassfish3/glassfish

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

### Glassfish starten

    [root@intapprod data]# service glassfish start

### Admin Passwort ändern

Nach der Installation muss das Passwort geändert werden.
Greift man jedoch per Browser auf die Glassfish Admin Console 
([https://server:4848](http://server:4848)) bekommt man folgende Fehlermeldung:

> Secure Admin must be enabled to access the DAS remotely.

Hierzu muss der Befehl `enable-secure-admin` ausgeführt werden. 
Allerdings geht dies nicht, solange das *admin* Passwort leer ist. 
Das admin Passwort kann wie folgendermaßen geändert werden:

    [root@intapprod data]# cd /data/glassfish3/bin
    [root@intapprod bin]# JAVA_HOME=/data/java/jdk1.7.0_13
    [root@intapprod bin]# export JAVA_HOME
    [root@intapprod bin]# PATH=$JAVA_HOME/bin:$PATH
    [root@intapprod bin]# export PATH
    [root@intapprod bin]# ./asadmin
    Use "exit" to exit and "help" for online help.
    asadmin> change-admin-password \--user admin
    Enter admin password>   <Enter>
    Enter new admin password>   <Password>
    Enter new admin password again>   <Password>
    Command change-admin-password executed successfully.

Nun kann der Befehl `enable-secure-admin` ausgeführt werden

    \[\]# ./asadmin \--host localhost \--port 4848 enable-secure-admin
    Enter admin user name>  admin
    Enter admin password for user "admin">  <Bahnhofsuhr>
    You must restart all running servers for the change in secure admin to take effect.
    Command enable-secure-admin executed successfully.

    \[\]# /etc/init.d/glassfish stop
    Waiting for the domain to stop ....
    Command stop-domain executed successfully.
    \[\]# /etc/init.d/glassfish start
    Waiting for domain1 to start ..........
    Successfully started the domain : domain1
    domain  Location: /data/glassfish3/glassfish/domains/domain1
    Log File: /data/glassfish3/glassfish/domains/domain1/logs/server.log
    Admin Port: 4848
    Command start-domain executed successfully.

Anschließend ist eine Anmeldung an der Admin Console [https://server:4848](https://server:4848)
möglich.

### MySQL Treiber installieren

Hierzu muss der MySQL Treiber ins das Glassfish Module Verzeichnis kopiert werden. 
Den Treiber kann man sich per `wget`von [http://dev.mysql.com/downloads/connector/j/5.1.html](http://dev.mysql.com/downloads/connector/j/5.1.html) herunterladen.

    [root@intapprod ~]# cd /data
    [root@intapprod data]# /etc/init.d/glassfish stop
    [root@intapprod data]# wget http://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.23.zip/from/http://cdn.mysql.com/
    [root@intapprod data]# unzip mysql-connector-java-5.1.23.zip
    [root@intapprod data]# cd mysql-connector-java-5.1.23
    [root@intapprod mysql-connector-java-5.1.23]# mv mysql-connector-java-5.1.23-bin.jar /data/glassfish3/glassfish/domains/domain1/lib/
    [root@intapprod mysql-connector-java-5.1.23]# cd /data
    [root@intapprod data]# /etc/init.d/glassfish start

### Einrichtung des Connection Pools

Die Einrichtung der Datenquellen (Connection Pool, JDBC Resources) kann 
basierend auf folgender Anleitung vorgenommen werden: [http://netbeans.dzone.com/connection-pooling-glassfish-nb](http://netbeans.dzone.com/connection-pooling-glassfish-nb)

Bei der Einrichtung eines *Connection Pool*s sind folgende Paramter notwendig:

Name         | Value
-------------|:---------------------------------------
User         | root
DatabaseName | \<dbname\>
Password     | \<password\>
URL          | `jdbc:mysql://localhost:3306/<dbname>`
Url          | `jdbc:mysql://localhost:3306/<dbname>`
ServerName   | localhost

Der Zugriff per `root` User auf die Datenbank ist natürlich nicht ideal. Hier 
sollte besser ein eigener Datenbankuser angelegt werden, der nur auf die
entsprechende Datenbank Zugriff hat.  
Die beiden Parameter *URL* und *Url* müssen beide angegeben werden.

