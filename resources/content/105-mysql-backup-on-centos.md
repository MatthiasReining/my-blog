title=MySQL Backup auf einem Linux System
subtitle=Rollierte Backups
created=2013-02-19T21:03:11
tags=MySQL, Backup, Linux, Shell, Skript, Windows
language=de

<img src="http://blog.matthias-reining.com/img/article-images/backup.png" style="float: right;">
In dem Artikel [Server-Setup: Linux, Java, MySQL und Glassfish](http://blog.matthias-reining.com/serversetup-linux-java-mysql-und-glassfish/)
wurde unter anderem das Setup einer MySQL Datenbank beschrieben. Was in dem Artikel
allerdings unterschlagen worden ist, ist ein passendes Backup. Eine mögliche 
Variante wird im folgendem beschrieben:

Im hier beschriebenen Szenario wird die MySQL auf einem Linux/CentOS System betrieben. 
Ein Backup ist allerdings nur auf einem Windows Filesystem möglich, weshalb ein 
entsprechender Windows-Share gemountet wird (siehe [hier](http://blog.matthias-reining.com/cronjob-fehlermeldung--mountcifs-command-not-found/)).
Als Backup werden MySQL Dumps der letzten 5 Tage gesichert (dies ist natürlich nur 
möglich, solange keine riesigen Datenbanken vorliegen).

Das Skript geht wie folgt vor:

* Backup-Dateiname definieren inkl. Datumsangabe (`$(date +"%Y-%m-%d")`)

* mounten des Windows-Shares (`mount.cifs`)

* vorhandene Backupfiles (des gleichen Tages) löschen

* MySQL Dump erstellen (`mysqldump`)

* Dump-SQL-File packen (`gzip`)

* Datum ermitteln um Dumps älter als 5 Tage zu ermitteln (`$(date --date="-5 day" +"%Y-%m-%d")`)

* Dumps älter als 5 Tage löschen (`for`-Schleife)

* Windows-Share unmounten (`umount`)


Und so sieht das Skript aus:

    #!/bin/bash
    BACKUP_DIR="/data/windowsShare"
    MYSQL_USER="root"
    MYSQL_PASS="<password>"
    NOW=$(date +"%Y-%m-%d")
    FILENAME="$NOW-mysql-dump-intappprod.sql"
    DUMPFILE="$BACKUP_DIR/$FILENAME"
    # mount Windows share
    /sbin/mount.cifs //192.168.1.28/Backup $BACKUP_DIR/ -o user=backup,password=<share-password>
    # Backup files.
    rm -f $DUMPFILE $DUMPFILE.gz
    mysqldump --all-databases -u$MYSQL_USER -p$MYSQL_PASS > $DUMPFILE
    gzip $DUMPFILE
    REMOVE_BEFORE=$(date --date="-5 day" +"%Y-%m-%d")
    echo "Remove all files before $REMOVE_BEFORE"
    for filename in $BACKUP_DIR/*;
    do
    dt_file=`echo $filename | grep -o -E '[12][0-9]{3}(-[0-9]{2}){2}'`
    # \< less than for strings
    if [ "$dt_file" \< "$REMOVE_BEFORE" ]; then
    echo "remove $filename"
    rm -f $filename
    fi
    done;
    #unmout
    /bin/umount $BACKUP_DIR

Wenn das Script angelegt wird, muss es anschließend natürlich noch als *ausführbar*
gekennzeichnet werden:

    [root@sourcecoding scripts]# chmod a+x backup2share.sh

Anschließend sollte das Script noch täglich per *cron*-Job gestartet werden.

    [root@sourcecoding scripts]# crontab -e

Der *cron*-Job kann wie folgt definiert werden um täglich um 2Uhr Nachts ein Backup
auf dem Windows Share zu erstellen.

    # backupjob um die alle mysql daten im Windows-Backup zu sichern 
    0 2 * * * /data/scripts/backup2share.sh

