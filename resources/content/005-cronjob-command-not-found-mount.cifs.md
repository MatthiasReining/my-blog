title=Cronjob Fehlermeldung - mount.cifs: command not found
subtitle=Das unerkennbare Icon aus diesem Blog...
created=2013-02-13T13:29:16
tags=troubleshooting, cron, cronjob, mount.cifs, mount, linux, command not found
language=de

Ich habe ein kleines Backupscript auf einem CentOS Linux System erstellt, welches
Files auf ein Windowsshare kopieren soll. Hierzu habe verwende ich den Befehl `mount.cifs`.

Die *cifs* Tools müssen zuvor installiert werden:

	yum install cifs-utils

Anschließend lässt sich mit folgendem Befehl ein Windows-Verzeichnis mounten

	mount.cifs //192.168.1.28/Backup /data/backup/ -o user=<user>,password=<password>
  
  
Das Backup-Script wird per Cronjob ausgeführt:

	[root@intappprod scripts]# crontab -l
	0 2 * * * /data/scripts/backup2share.sh

Beim Ausführen kommt es zum Fehler **mount.cifs: command not found** (kann per `mail` eingesehen werden).

	[root@intapprod scripts]# mail
	Heirloom Mail version 12.4 7/29/08.  Type ? for help.
	"/var/spool/mail/root": 3 messages
	>   1 Cron Daemon           Wed Feb 13 02:00  26/1051  "Cron <root@intapprod>"
	& 1
	Message  1:
	...
	
	/data/scripts/backup2share.sh: line 12: mount.cifs: command not found
	umount: /data/windowsShare: not mounted

Das mounten funktioniert nicht :-(

In einem Script das per Cronjob ausgeführt wird, müssen die Pfade zum mount.cifs 
Programm **absolut** angegeben werden (**/sbin/mount.cifs** und **/bin/umount**).

Auszug aus dem funktionierenden Backup-Script:

	# mount Windows share
	/sbin/mount.cifs //192.168.1.28/Backup /data/backup/ -o user=<user>,password=<password>
	
	...
	
	/bin/umount /data/backup

