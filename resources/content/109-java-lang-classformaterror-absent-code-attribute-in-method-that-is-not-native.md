title=java.lang.ClassFormatError: Absent Code attribute in method that is not native or abstract in class file
subtitle=JavaEE API ohne Implementierung...
created=2013-03-29T13:15:10
tags=Java EE, Troubleshooting, maven
language=de

Folgende Exception habe ich bei einem **Test** einer JavaEE Applikation bekommen:

`java.lang.ClassFormatError: Absent Code attribute in method that is not native or abstract in class file javax/ws/rs/WebApplicationException` 

Der Code funktioniert aber auf dem Server tadellos. Was ist also der Grund für diese Exception?

Die Erklärung liegt an der Maven Konfiguration für mein JavaEE Projekt. Für die JavaEE Abhängigkeiten ist die Bibliothek `java-web-api` als `provided` eingebunden - standardmäßig bei der Nutzung eines JavaEE maven archetypes.

		<dependency>
            <groupId>javax</groupId>
            <artifactId>javaee-web-api</artifactId>
            <version>6.0</version>
            <scope>provided</scope>
        </dependency>

Bei diesem Jar File handelt es sich allerdings lediglich um eine API. Also primär Interface-Klassen *ohne* Implementierung. Dies ist im Prinzip auch sehr gut, da die konkrete Implementierung auf jedem AppServer ja anders aussehen kann!  
Oracle hat beim Paken dieses Jar Files einfach alle Implementierungen weggelassen (Methoden-Body) - auch bei abstrakte Klassen. 
Zum kompilieren ist dies auch kein Problem; wird allerdings das JAR File in einer  Anwendung verwendet, wie hier im Unit-Test, führt dies zu Problemen/Fehleren!


Bei mir konkret war es eine Unit-Test mit einem Jersey-Client der einen JSON Service gegen einen laufenden Server testet (also eigentlich kein *echter* Unit-Test ;-)).

		<dependency>
			<groupId>com.sun.jersey</groupId>
			<artifactId>jersey-json</artifactId>
			<version>1.17.1</version>
		</dependency>
		
		<dependency>
			<groupId>com.sun.jersey</groupId>
			<artifactId>jersey-client</artifactId>
			<version>1.17.1</version>
			<scope>test</scope>
		</dependency>
		

Der Test nutzt daher auch die Klassen auf der `javaee-web-api`.

Um dieses Problem zu lösen gibt es verschiedene Ansätze: 

* JBoss bietet eine alternative Implementierung zu `javaee-web-api`an, die dieses Problem nicht hat.

		<dependency>
		   <groupId>org.jboss.spec</groupId>
		   <artifactId>jboss-javaee-6.0</artifactId>
		   <version>1.0.0.Final</version>
		   <type>pom</type>
		   <scope>provided</scope>
		</dependency>

* Man weist dem Unit-Test an, diese Klassen nicht zu verwenden

		<plugin>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.14</version>
            <configuration>
                <classpathDependencyExcludes>
                    <-- exclude code absent api -->
                    <classpathDependencyExclude>javax:javaee-api</classpathDependencyExclude>
                    <classpathDependencyExclude>javax:javaee-web-api</classpathDependencyExclude>
                </classpathDependencyExcludes>
            </configuration>
        </plugin>


Welche Variante man wählt ist natürlich Geschmacksache.