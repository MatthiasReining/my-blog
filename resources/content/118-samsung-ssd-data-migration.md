title=Samsung Data Migration & BitLocker - Access violation at address xyz in module 'Restore.dll'
subtitle=Die kleinen Details...
created=2013-08-31T03:24:13
tags=Samsung SSD, Data Migration, BitLocker
language=de

In meinem tollen Thinkpad Twist Notebook habe ich die bestehende 128GB SSD Platte mit einer neuen 500GB Samsung SSD 840 ausgetauscht.

![Thinkpad Twist](http://blog.matthias-reining.com/img/article-images/thinkpad-twist.jpg "Thinkpad Twist")
![Samsung SSD 840](http://blog.matthias-reining.com/img/article-images/samsung-ssd-840.jpg "Samsung SSD 840").

Samsung liefert hierzu eine fertige Migrationssoftware ([Samsung Data Migration](http://www.samsung.com/global/business/semiconductor/samsungssd/downloads.html "Samsung Data Migration")) mit, mit der es Möglich ist die bestehende Festplatte zu klonen. Hierzu gibt es jede Menge Blogeinträge wie toll das alles klappt!

Das ganze hat bei mir natürlich erstmal nicht geklappt :-( !!!

#### Fehlermeldung

Ich habe folgende Fehlermeldung kurz nach dem Start des Klonvorgangs bekommen:
 

	Access violation at address xyz in module 'Restore.dll'.

#### Ursache

Nachdem ich gefühlte 100mal einfach den Klon-Button gedrückt habe, hat das Ganze natürlich auch nicht funktioniert... Also hab ich mir dann doch mal die Details zu der Migrations-Software angeschaut:

[Samsung Data Migration - User Manual](http://www.samsung.com/global/business/semiconductor/samsungssd/downloads/Samsung_SSD_Data_Migration_User_Manual_German_v2.pdf)

Punkt 8 der Beschränkungen:

> Diese Software kann keine verschlüsselten Datenträger klonen. Um einen 
> verschlüsselten Datenträger zu klonen, müssen Sie zuerst das Passwort des 
> Datenträgers entfernen.

#### Lösung

BitLocker deaktivieren und dann geht's auch...

Hätte man sich natürlich gleich denken können, allerdings ist die Fehlermeldung hier auch nicht wirklich hilfreich!

#### Fazit

RTFM -> Obwohl ich eigentlich mehr Fan von aussagekräftigen Fehlermeldungen bin ;-) 