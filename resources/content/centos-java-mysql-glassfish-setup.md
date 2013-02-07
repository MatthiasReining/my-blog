title=CentOS, Java, MySQl und Glassfish - Setup
subtitle=Ein Glassfish Setup
created=2013-02-07T18:13:00
tags=Java, MySQL, Glassfish, Setup

# Server Setup

## VM

Als Ausgangslage dient eine vom TPS zur Verfügung gestellte Standard VM mit CentOS als OS. Bei CentOS handelt es sich um einen RHEL clone (RedHat Enterprise Linux).

    [root@intapprod ~]# cat /etc/*-release
    CentOS release 6.3 (Final)
    CentOS release 6.3 (Final)
    CentOS release 6.3 (Final)

##Datenbank MySQL

Als Datenbank dient ein MySQL Server.
Die Installation orientiert sich an folgender Anleitung: http://dokuwiki.nausch.org/doku.php/centos:mysql

**Installation**

    [root@intapprod ~]# yum install mysql-server -y

**Check der Konfiguration**

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

**DB Server starten**

    [root@intapprod ~]# service mysqld start

**Beim Start der VM DB Server automatisch starten**

    [root@intapprod ~]# chkconfig mysqld on

**Server absichern**

    [root@intapprod ~]# /usr/bin/mysql_secure_installation
    ...
    Enter current password for root (enter for none):  <Enter>
    ...
    Set root password? [Y/n] <y>
    ...
    New password: <Bahnhofsuhr>
    Re-enter new password: <Bahnhofsuhr>
    ...
    Remove anonymous users? [Y/n] <y>
    ...
    Disallow root login remotely? [Y/n] <y>
    ...
    Remove test database and access to it? [Y/n] <y>
    ...
    Reload privilege tables now? [Y/n] <y>

**Datenbank Schematas und Backup einspielen**

Die DB Skripte und das Backup ist als "einfacher" SQL Dump vorhanden. Dieser kann wie folgt eingespielt werden.

    [root@intapprod ~]# mysql -u root -p < 2013-01-17-Thursday.sql

Der Importvorgang zeigt nichts auf der Konsole an. Folgender Befehl zeigt an, ob zumindest die Datenbanken angelegt worden sind.

    [root@intapprod ~]# mysql -u root -p
    ...
    mysql> show databases;
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | litapp             |
    | mysql              |
    | ncbay              |
    | ncbusinesscards    |
    | schulung           |
    +--------------------+
    6 rows in set (0.00 sec)

## Java installieren

Um maximale Flexibilität zu haben wird das JDK ohne Packagemanager installiert:
Die Installation orientiert sich an der Oracle Anleitung (http://docs.oracle.com/javase/7/docs/webnotes/install/linux/linux-jdk.html)

Nachdem für den JDK Download auf der Oracle Webseite die Lizenzbedingungen bestätigt werden müssen, ist ein einfacher Download per wget nicht möglich. Das JDK kann aber über einen Browser heruntergeladen werden (http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) und anschileßend per (Win)SCP auf den Server kopiert werden.

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

Als Appserver wird Oracle Glassfish Open Source Edition eingesetzt. Die Installation orientiert sich (sehr grob) an http://www.davidghedini.com/pg/entry/install_glassfish_3_1_on.

    [root@intapprod data]# wget http://download.java.net/glassfish/3.1.2.2/release/glassfish-3.1.2.2.zip
    [root@intapprod data]# unzip glassfish-3.1.2.2.zip

**Startskript für Glassfish**

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

**Skript Ausführbar machen**

    [root@intapprod data]# chmod 755 /etc/init.d/glassfish
    [root@intapprod data]# chkconfig --add /etc/init.d/glassfish
    [root@intapprod data]# chkconfig --level 234 glassfish on

**Glassfish starten**

    [root@intapprod data]# service glassfish start

**Admin Passwort ändern**

Nach der Installation muss das Standardpasswort "adminadmin" geändert werden.
Greift man jedoch per Browser auf die Glassfish Admin Console (http://intappprod.wue.nobiscu:4848) bekommt man folgende Fehlermeldung:

Secure Admin must be enabled to access the DAS remotely.

Hierzu muss der Befehl enable-secure-admin ausgeführt werden. Allerdings geht dies nicht, solange das "admin" Passwort leer ist. Das admin Passwort kann wie folgendermaßen geändert werden

    [root@intapprod data]# cd /data/glassfish3/bin
    [root@intapprod bin]# JAVA_HOME=/data/java/jdk1.7.0_13
    [root@intapprod bin]# export JAVA_HOME
    [root@intapprod bin]# PATH=$JAVA_HOME/bin:$PATH
    [root@intapprod bin]# export PATH
    [root@intapprod bin]# ./asadmin
    Use "exit" to exit and "help" for online help.
    asadmin> change-admin-password \--user admin
    Enter admin password>   <Enter>
    Enter new admin password>   <Bahnhofsuhr>
    Enter new admin password again>   <Bahnhofsuhr>
    Command change-admin-password executed successfully.

Nun kann der Befehl enable-secure-admin ausgeführt werden

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

Anschließend ist eine Anmeldung an der Admin Console (https://intappprod.wue.nobiscum:4848/) mit admin/<Bahnhofsuhr> möglich.

**MySQL Treiber installieren**

Hierzu muss der MySQL Treiber ins das Glassfish Module Verzeichnis kopiert werden. Den Treiber kann man sich von http://dev.mysql.com/downloads/connector/j/5.1.html herunterladen.

    [root@intapprod ~]# cd /data
    [root@intapprod data]# /etc/init.d/glassfish stop
    [root@intapprod data]# wget http://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.23.zip/from/http://cdn.mysql.com/
    [root@intapprod data]# unzip mysql-connector-java-5.1.23.zip
    [root@intapprod data]# cd mysql-connector-java-5.1.23
    [root@intapprod mysql-connector-java-5.1.23]# mv mysql-connector-java-5.1.23-bin.jar /data/glassfish3/glassfish/domains/domain1/lib/
    [root@intapprod mysql-connector-java-5.1.23]# cd /data
    [root@intapprod data]# /etc/init.d/glassfish start

Die Einrichtung der Datenquellen (Connection Pool, JDBC Resources) kann basierend auf folgender Anleitung vorgenommen werden: http://netbeans.dzone.com/connection-pooling-glassfish-nb

Schulungsdatenbank:

JDBC Connection Pool: SchulungsPool
JDBC Resource "jdbc/_trainingPool"

Folgende Parameter wurden für den SchulungsPool angegeben:

| Name         | Value                                |
|:-------------|-------------------------------------:|
| User         | root                                 |
| DatabaseName | schulung                             |
| Password     | <Bahnhofsuhr>                        |
| URL          | jdbc:mysql://localhost:3306/schulung |
| Url          | jdbc:mysql://localhost:3306/schulung |
| ServerName	 localhost                            |

