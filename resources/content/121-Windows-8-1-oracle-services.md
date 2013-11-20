title=Oracle Services are Missing after Update to Windows 8.1 
subtitle=Windows 8.1 crashed my Oracle database
created=2013-11-20T16:58:33
tags=Oracle, Oracle XE, Windows 8.1, Oracle Services, Recovery
language=en

First, I love Windows and I love Windows 8 and 8.1, too. Of course for servers I will use Linux, but on a client system with UI: Windows is great!

is great... in general ;-)

After the update for Windows 8.1 all my Oracle XE services where lost :-( I don't know why, maybe MS thinks I don't use the Oracle stuff...

With following commands you can restore your Oracle XE services:

	E:\oraclexe\app\oracle\product\11.2.0\server\bin>oradim -new -sid XE -startmode
	manual -spfile
	E:\oraclexe\app\oracle\product\11.2.0\server\bin>oradim -edit -sid XE -startmode
	 auto -srvcstart system

Get the listener status

	E:\oraclexe\app\oracle\product\11.2.0\server\bin>lsnrctl
	
	LSNRCTL for 32-bit Windows: Version 11.2.0.2.0 - Production on 20-NOV-2013 16:52
	:45
	
	Copyright (c) 1991, 2010, Oracle.  All rights reserved.
	
	Welcome to LSNRCTL, type "help" for information.
	
	LSNRCTL> status
	Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=IPC)(KEY=EXTPROC1)))
	TNS-12541: TNS:no listener
	 TNS-12560: TNS:protocol adapter error
	  TNS-00511: No listener
	   32-bit Windows Error: 2: No such file or directory
	Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=nbr00048.italrgi.it)(POR
	T=1521)))
	TNS-12541: TNS:no listener
	 TNS-12560: TNS:protocol adapter error
	  TNS-00511: No listener
	   32-bit Windows Error: 61: Unknown error


Try to start the listener

	LSNRCTL> start
	Starting tnslsnr: please wait...
	
	Failed to open service <OracleXETNSListener>, error 1060.
	TNSLSNR for 32-bit Windows: Version 11.2.0.2.0 - Production
	System parameter file is E:\oraclexe\app\oracle\product\11.2.0\server\network\ad
	min\listener.ora
	Log messages written to E:\oraclexe\app\oracle\diag\tnslsnr\nbr00048\listener\al
	ert\log.xml
	Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(PIPENAME=\\.\pipe\EXTPROC1ipc
	)))
	Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=nbr00048.italrgi.it)(POR
	T=1521)))
	
	Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=IPC)(KEY=EXTPROC1)))
	STATUS of the LISTENER
	------------------------
	Alias                     LISTENER
	Version                   TNSLSNR for 32-bit Windows: Version 11.2.0.2.0 - Produ
	ction
	Start Date                20-NOV-2013 16:53:39
	Uptime                    0 days 0 hr. 0 min. 5 sec
	Trace Level               off
	Security                  ON: Local OS Authentication
	SNMP                      OFF
	Default Service           XE
	Listener Parameter File   E:\oraclexe\app\oracle\product\11.2.0\server\network\a
	dmin\listener.ora
	Listener Log File         E:\oraclexe\app\oracle\diag\tnslsnr\nbr00048\listener\
	alert\log.xml
	Listening Endpoints Summary...
	  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(PIPENAME=\\.\pipe\EXTPROC1ipc)))
	  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=nbr00048.italrgi.it)(PORT=1521)))
	Services Summary...
	Service "CLRExtProc" has 1 instance(s).
	  Instance "CLRExtProc", status UNKNOWN, has 1 handler(s) for this service...
	Service "PLSExtProc" has 1 instance(s).
	  Instance "PLSExtProc", status UNKNOWN, has 1 handler(s) for this service...
	
	The command completed successfully
	LSNRCTL> ^C


Create the listener as Windows service again

	E:\oraclexe\app\oracle\product\11.2.0\server\bin>sc create OracleXETNSListener
	
	E:\oraclexe\app\oracle\product\11.2.0\server\bin>net start OracleXETNSListener
	Der angeforderte Dienst wurde bereits gestartet.


Now APEX is available agin :-) [http://127.0.0.1:1158/apex](http://127.0.0.1:1158/apex)