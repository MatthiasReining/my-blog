title=RESTful Services auf dem AppServer aktivieren.
subtitle=JAX-RS per @ApplicationPath ohne web.xml aktivieren
created=2013-03-10T22:25:15
tags=Java EE, RESTful, REST, web.xml, ApplicationPath, JAX-RS, JSR 311, Java API for RESTful Web Services, 
language=de

 
Alle Java EE 6 Server müssen den [JSR 311](http://jsr311.java.net/) implementieren. Dieser besagt einfach das es eine *Java API for RESTful Web Services* geben muss, kurz *JAX-RS*.  
Um REST Servies zu verwenden, müssen diese auf dem Application Server allerdings erst noch *aktiviert* werden. Dies kann man entweder über einen Eintrag in der `web.xml` machen oder programmatisch mit Hilfe der Annotation [`ApplicationPath`](http://docs.oracle.com/javaee/6/api/javax/ws/rs/ApplicationPath.html). Der programmatische Ansatz mit der Annotation **ApplicationPath** ist hierbei jedoch zu bevorzugen.

Der Eintrag in der `web.xml` sieht wie folgt aus:


	<servlet>
    	<display-name>JAX-RS REST Servlet</display-name>
    	<servlet-name>REST-Servlet</servlet-name>
    	<servlet-class>com.sun.jersey.spi.container.servlet.ServletContainer</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>REST-Servlet</servlet-name>
		<url-pattern>/rest/*</url-pattern>
	</servlet-mapping>

In diesem Beispiel wird für die Implementierung des REST Services die JAX-RS  Referenzimplementierung [*Jersey*](http://jersey.java.net/) definiert, welche unter anderem im Application Server [Glassfish](http://glassfish.java.net/) Verwendung findet.

Dieser Ansatz ist aus meiner Sicht schlecht, da hier die Implementierung fix in der `web.xml` hinterlegt ist. Die Applikation (das *war*-File) lässt sich so nicht mehr einfach auf einen anderen Application Server ausführen, da nicht festgelegt ist, welche JAX-RS Implementierung auf diesem zum Einsatz kommt. Beispielswese verwendet der [JBoss Application Server](http://www.jboss.org/jbossas) als REST Implementierung [*RESTeasy*](http://www.jboss.org/resteasy/).

Ein besserer Ansatz ist die Definition mit der Annotation [`ApplicationPath`](http://docs.oracle.com/javaee/6/api/javax/ws/rs/ApplicationPath.html). Hierzu muss lediglich eine Klasse im Classpath vorhanden sein mit entsprechender Annotation.

	import javax.ws.rs.ApplicationPath;
	import javax.ws.rs.core.Application;
	
	@ApplicationPath("rest")
	public class RestConfig extends Application {
	}

Der *Path* wird hier als *value* der Annotation definiert, analog dem `url-pattern` aus der *web.xml*.


Das Setup eines REST Services geht auf einem Application Server also auch **ohne** einen `web.xml`-Eintrag! Für viele dürfte dies nichts neues sein, weshalb sich die Frage stellt was dieser Artikel überhaupt soll: 

Wieso trotz der Nachteile die Deklaration in der *web.xml* immer wieder verwendet wird, hat vielleicht zum Grund, dass es viele JAX-RS Beispiele im Netz für den Servlet Container *Tomcat* oder *Jetty* gibt. Nachdem es sich bei diesen aber um keine vollwertigen Application Server handelt, ist hier auch keine Deklaration per *ApplicationPath* möglich.

Daher gilt: **auf dem AppServer JAX-RS immer per ApplicationPath definieren!**