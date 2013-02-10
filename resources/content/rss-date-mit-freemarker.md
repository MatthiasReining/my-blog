title=RSS Datum mit Freemarker
subtitle=Datumsformatierung mit Freemarker
created=2013-02-10T19:42:19
tags=RSS Feed, RSS, Freemarker, RFC 822
language=de

In einem RSS Feed befinden sich verschiedenen Datumsangaben (`pubDate` und 
`lastBuildDate`). Alle Datumsangaben in einem RSS Feed müssen sich an
die *Date-Time* Spezifikation [RFC 822](http://asg.web.cmu.edu/rfc/rfc822.html) 
halten. Eine Datum muss dann entsprechend folgendermaßen formatiert sein:

    Sun, 10 Feb 2013 20:12:46 GMT

## Datumswerte mit Freemarker 

Um Datumswerte mit der Templateengine [Freemarker](http://freemarker.sourceforge.net/) 
zu erstellen, gibt es verschiedene Möglichkeiten.  
Freemarker bietet unter anderem fertige Build-In Formate zur Darstellung von 
Datumswerten an (siehe [http://freemarker.sourceforge.net/docs/ref_builtins_date.html](http://freemarker.sourceforge.net/docs/ref_builtins_date.html)).

    Erstelldatum (ISO UTC): ${entry.created?datetime?iso_utc}
                 (Ausgabe): 2013-02-10T20:02:13Z
                 
    Erstelldatum: ${entry.created?date?string.long}
       (Ausgabe): Februar 10, 2013 20:02:14 PM CEST

Leider ist das RFC 822 Format kein fertiger *Build-In* Formatierer.  
Mit folgendem Pattern lässt sich ein Datumswert im RFC 822 Format erstellen:

    <pubDate>${createdTime?datetime?string("EEE, dd MMM yyyy HH:mm:ss z")}</pubDate>

Hierbei ist allerdings zu beachten, dass bei der Verwendung von *EEE* und *z* 
*local*-Einstellungen des generierenden Systems per default verwendet werden. 
Diese müssen daher **fix** gesetzt werden, so dass keine lokalen Einstellungen 
herangezogen werden:

    templateConfiguration.setLocale(Locale.US);
    templateConfiguration.setTimeZone(TimeZone.getTimeZone("GMT"));

Mit der *locale* Einstellung wird sichergestellt, dass der Wochentag im englischem
Format angegeben wird (MON, THU, WEN, ...). Mit `setTimeZone` wird die Zeitzone
fix auf *GMT* gesetzt.
