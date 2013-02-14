title=PL/SQL Developer - Initialization error | Windws 7 - 64bit
subtitle=Installationsprobleme beim PL/SQL Developer
created=2010-06-01T19:13:11
tags=Troubleshooting, PL/SQL Developer, Initalization error
language=de

Auf meinem neuen Windows 7 – 64bit System habe ich ein tolles Oracle 11g – 64bit installiert – Funzt perfekt!

Anschließend noch den PL/SQL Developer von  allroundautomations installiert und schon gingen die Probleme los…

**Vorab: das Problem liegt daran, dass der PL/SQL Developer ein 32bit Programm ist und Probleme bei der Verwendung des 64bit Oracle Treibers (Client) hat.**

Zuerst kam bei der Anmeldung folgender Fehler:

<pre style="font-size:14px">
    Initialization error
    SQL*Net not properly installed

    OracleHomeKey:
    OracleHomeDir:
</pre>

Nach etwas googlen bin ich auf Hinweis gekommen, bei denen man spezielle Registryeinträge ändern muss oder innerhalb vom PL/SQL Developer unter Tools/Preferences direkt Einstellungen vornehmen usw... Hat alles nichts geholfen :-(

Auf der Console habe ich dann mal manuell mein ORACLE_HOME Verzeichnis gesetzt. Das Ergebnis war nun folgende Fehlermeldung:

<pre style="font-size:14px">
    E:\PLSQL Developer>set ORACLE_HOME=E:\oracle\app\mre\product\11.2.0\dbhome_1
    E:\PLSQL Developer>plsqldev.exe

    Initialization error
    Could not load „E:\oracle\app\mre\product\11.2.0\dbhome_1\bin\oci.dll“

    OracleHomeyKey:
    E:\oracle\app\mre\product\11.2.0\dbhome_1
    Found: oci.dll
    Using: E:\oracle\app\mre\product\11.2.0\dbhome_1\bin\oci.dll
    LoadLibrary(E:\oracle\app\mre\product\11.2.0\dbhome_1\bin\oci.dll) returned 0
</pre>

Geht also auch nicht :-(

Google hat bei eingeschränktere Suche irgendwie nur noch Seiten auf Chinesisch angezeigt (das Übersetzungstool von google funzt hier sehr cool!). Also bin ich so auf einer chinesischen Seite auf die 64bit / 32bit Oracle Client Problematik gestoßen. Anschließend habe ich dann gezeilt auf der Page von allroundautomations nach 64 bit gesucht und habe folgenden Treffer gefunden:

[http://www.allroundautomations.com/threads/ubbthreads.php?ubb=showflat&Number=35365](http://www.allroundautomations.com/threads/ubbthreads.php?ubb=showflat&Number=35365)

**Ein passender Oracle Client muss also her!**

Als erstes also einen Windows 32bit Oracle Client herunterladen – Ich habe mich hier für den Instant Client Version 11.2.0.1.0 entschieden.

[http://www.oracle.com/technology/software/tech/oci/instantclient/htdocs/winsoft.html](http://www.oracle.com/technology/software/tech/oci/instantclient/htdocs/winsoft.html)

Diesen kann man einfach entpacken und braucht keine extra Installation.

Anschließend müssen diese Parameter noch  im PL/SQL Developer bekannt gemacht werden.

Oracle-->Preferences-->Oracle-->Connection  
**Oracle Home:**  [leer lassen, wird durch Environment Variable automatisch gezogen]  
**OCI library:** `E:\instantclient_11_2\oci.dll`

Anschließend den PL/SQL Developer neu starten.

Jetzt kann der PL/SQL Developer wieder sauber seine Dienste mir zur Verfügung stellen :-)