title=ODBC Datenquellen in Windows 8 - Der 32/64Bit Unterschied
subtitle=Unterschiede bei der Definition der ODBC Datenquelle
created=2013-04-05T23:18:40
tags=Windows 8, Win8, ODBC, ODBC Datenquellen
language=de

Unter Windows 8 gibt es bei der Einrichtung von ODBC Datenquellen die Unterschiedung zwischen 32 und 64 Bit. Dies geht soweit, dass 64Bit Datenquellen sich nur mit dem "64Bit Datenquellen Administrator" bearbeiten lassen und 32Bit Datenquellen mit dem "32Bit Datenquellen Administrator". Je nach Datenbanktreiber ist hier auch die entsprechende Datenquelle zu wählen. Für Oracle gibt es beispielsweise einen Treiber für 32 und 64Bit.

Beim Aufruf der ODBC-Administration wird in Windows 8 per Default die 64Bit Variante aufgerufen.

<img src="http://blog.matthias-reining.com/img/article-images/win8-odbc-connection-launch.png" />

In dieser werden die 32Bit Datenquellen zwar angezeigt, aber können nicht bearbeitet werden.

<img src="http://blog.matthias-reining.com/img/article-images/win8-odbc-connection-64bit-administration.png" />

Möchte man eine 32Bit Datenquelle bearbeiten geht dies nur in der "32Bit Datenquellen Administration".  
Diese kann wie folgt aufgerufen werden:


	C:\Users\Matthias>%SystemDrive%
	C:\Users\Matthias>cd %SystemRoot%
	
	C:\Windows>REM start 32bit odbc datasource administration
	C:\Windows>SysWOW64\odbcad32.exe
	
	C:\Windows>REM start 64bit odbc datasource administration
	C:\Windows>System32\odbcad32.exe


<img src="http://blog.matthias-reining.com/img/article-images/win8-odbc-connection-32bit-administration.png" />
















