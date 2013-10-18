title=Backup a Linux Server to Dropbox
subtitle=How to Backup your Server (MySQL and Configuration) to Dropbox?
created=2013-09-20T15:42:10
tags=Backup, Linux, Dropbox, MySQL, crontab
language=en

In this blog post I describe a way how to backup a MySQL database and configuration scripts from a Linux CentOS server to DropBox.

### Dump MySQL 

The following script snippet shows a possibility to make a backup from a MySQL server including all databases:

	MYSQL_SERVER="127.0.0.1"
	MYSQL_USER="root"
	MYSQL_PASS="yourpassword"
	
	NOW=$(date +"%Y-%m-%d_%k%M%S")

	SAVE_PATH='/tmp'
	# dump mysql
	MYSQL_DUMP_FILE=$SAVE_PATH/$NOW-mysql-database.sql
	echo dump mysql to $MYSQL_DUMP_FILE
	mysqldump -u $MYSQL_USER -h $MYSQL_SERVER -p$MYSQL_PASS --all-databases > "$MYSQL_DUMP_FILE"

### Package Files and Dump

The next snippet packages all necessary files and directory to a tar-file. Afterwards the file is also zipped with bzip.

	SAVE_FILE=$SAVE_PATH/$NOW-backup.tar
	# Paths
	PATH[0]='/data/script'
	PATH[1]='/etc/httpd/conf.d'
	PATH[2]=$mysql_dump_file
	
	for t in "${PATH[@]}"
	do
	    tar rfv $SAVE_FILE $t/
	    echo $t saved!
	done
	 
	bzip2 $save_file
	echo zipped $save_file
	
	rm -rf $mysql_dump_file
	echo remove $mysql_dump_file

Now all important data are in one zip file on the server. The next step is to upload this file to DropBox.

### Upload to Dropbox
  
For this purpose Andrea Fabrizi has an excellent script: Dropbox-Upload

[https://github.com/andreafabrizi/Dropbox-Uploader](https://github.com/andreafabrizi/Dropbox-Uploader "https://github.com/andreafabrizi/Dropbox-Uploader")

Follow this instruction to setup your DropBox configuration an authorization. This is done via a DropBox app and an authentication key. Andrea's script has an wizard to support you in configuration DropBox.

On my server the Dropbox Uploader script is saved to `/data/script/dropbox_uploader.sh`. Now it's easy to upload all the data from the zip file to Dropbox

	echo copy 2 dropbox $save_file.bz2
	/data/script/dropbox_uploader.sh upload $save_file.bz2
	
	eche remove local backup file $save_file.bz2
	rm -rf $save_file.bz2

### Create a cron job

Create a cronjob with `crontab -e` for a daily backup.

	5 0 * * * /data/script/server-backup.sh

### Links

The script and the post was inspired by 

* [http://cowthink.net/bash-automatisiertes-backup-per-dropbox-upload/](http://cowthink.net/bash-automatisiertes-backup-per-dropbox-upload/ "http://cowthink.net/bash-automatisiertes-backup-per-dropbox-upload/") 
* [http://cowthink.net/bash-automatisiertes-backup-per-dropbox-upload/](http://cowthink.net/bash-automatisiertes-backup-per-dropbox-upload/ "http://cowthink.net/bash-automatisiertes-backup-per-dropbox-upload/")


### Full Script

	MYSQL_SERVER="127.0.0.1"
	MYSQL_USER="root"
	MYSQL_PASS="password"
	
	
	NOW=$(date +"%Y-%m-%d_%k%M%S")
	echo timestamp = $NOW
	save_path='/tmp'
	save_file_base=$save_path/$NOW-sourcecoding-backup
	save_file=$save_file_base.tar
	
	# dump mysql
	mysql_dump_file=$save_path/$NOW-mysql-database.sql
	echo dump mysql to $mysql_dump_file
	mysqldump -u $MYSQL_USER -h $MYSQL_SERVER -p$MYSQL_PASS --all-databases > "$mysql_dump_file"
	
	echo backup to $save_file
	# Paths
	path[0]='/data/script'
	path[1]='/etc/httpd/conf.d'
	path[2]=$mysql_dump_file
	
	# Zip it
	for t in "${path[@]}"
	do
	    tar rfv $save_file $t/
	    echo $t saved!
	done

	bzip2 $save_file
	echo zipped $save_file
	
	rm -rf $mysql_dump_file
	echo remove $mysql_dump_file
	
	echo copy 2 dropbox $save_file.bz2
	/data/script/dropbox_uploader.sh upload $save_file.bz2
	
	eche remove local backup file $save_file.bz2
	rm -rf $save_file.bz2
	