title=JSF2.0 AJAX Support - jsf.js wird nicht geladen
subtitle=Eine oder mehrere Ressourcen haben das Ziel 'head', aber es wurde keine Komponente 'head' in der Ansicht definiert.
created=2010-01-09T05:14:13
tags=Java, JSF2, JSF2.0, AJAX, jsf.js, Troubleshooting

Bei meinem kleinem JSF Projekt ([https://kenai.com/projects/agrar-land](https://kenai.com/projects/agrar-land "https://kenai.com/projects/agrar-land")) hatte ich Probleme beim Einsatz von AJAX innerhalb meiner Facelets:


	<h:commandButton action="#{schlagView.addWorking}" value="add3">
	    <f:ajax render="@form" execute="@form" />
	</h:commandButton>

Eigentlich sollte beim Tag f:ajax alles automatisch passieren, sprich die benötigte JavaScript Library jsf.js sollte automatisch eingefügt werden.  
Leider hat das ganze auf den ersten Blick nicht wirklich funktioniert :-( Die Page hat einen ganz normalen submit gemacht - kein AJAX...  
Bei genauerem Hinschauen wurde allerdings folgende Meldung auf der HTML Page ausgegeben:


##### Eine oder mehrere Ressourcen haben das Ziel 'head', aber es wurde keine Komponente 'head' in der Ansicht definiert.

Der Grund hierfür war einfach, dass ich in meinem JSF Files nicht sauber gearbeitet habe.  
Ich habe für den HTML Header einfach normale HTML Tags verwendet - böses Faul wie sich heraus gestellt hat!!


**Damit AJAX funktioniert und somit das File `jsf.js` (früher `ajax.js`) automatisch eingebunden wird, muss mit dem JSF Tag `h:head` gearbeitet werden.**

Dann funktioniert auch alles :-)