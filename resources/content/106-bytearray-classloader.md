title=ByteArrayClassloader, Jar in Jar and executable WAR-Files
subtitle=Programme aus WAR-Files starten!
created=2013-02-24T18:01:12
tags=Jar in Jar, Executable WAR, ByteArrayClassLoader, ClassLoader, Byte Array Class Loader
language=de

Um von der Console aus eine Java Anwendung so einfach wie möglich zu nutzen, wäre es schön, alle benötigten Dateien und Abhängigkeiten zusammen in einem Paket zu haben; einem einzigem *JAR*-File (**J** ava **Ar** chive). Der Aufruf würde dann einfach per "`java -jar MyProgram.jar`" erfolgen und der Download von abhängigen Paketen und das lästige *Classpath* setzen würde ebenfalls entfallen.  
Im Web-Umfeld definiert die *JavaEE* Spezifikation mit dem *WAR*-File (**W** eb **Ar** chive) etwas vergleichbares. Allerdings funktioniert dies natürlich nur innerhalb eines Servlet Containers. Will man beim Start von der Console ein ähnliches Verhalten herbeiführen (Abhängigkeiten liegen alle in einem Paket/Archive), muss hierzu ein extra *ClassLoader* geschrieben werden.  
In diesem Artikel wird ein ClassLoader beschrieben, der es erlaubt Klassen direkt aus einem *WAR*-File von der Console aus aufzurufen.

## Struktur eines WAR/JAR-Files

In einem *WAR*-File sind per Definition Java Klassen im Ordner *WEB-INF/classes* und abhängige *JAR*-Files im Ordner *WEB-INF/lib*. Will man nun ein *WAR*-File von der Console aus starten, geht dies nur, in dem man dem Java System das *WAR*-File als *JAR*-File unterjubelt. Nachdem es sich bei beiden Archiven einfach um gepackte Dateien handelt, muss man hier auch nichts weiter machen. Klassen innerhalb eines *JAR*/*WAR*-Files können wie folgt gestartet werden:

    java -jar MyWebApp.war
    
Allerdings sucht der *Java ClassLoader* in einem *JAR*-File nur im root-Verzeichnis nach Klassen bzw. nach der *Java-Package* Ordernerstruktur. Damit Klassen unter *WEB-INF/classes* und in den *JAR*-Files unter *WEB-INF/lib* auch durchsucht werden, muss ein speziell dafür entwickelter *ClassLoader* eingesetzt werden.

## ByteArray-ClassLoader

Der *neue* ClassLoader ist sozusagen der Einstiegspunkt in das *WAR*-File. Daher muss dieser (die Klasse) auch vom *default* ClassLoader gefunden werden, was bedeuted, dass die Klasse direkt im *root*-Verzeichnis des *WAR*-Files liegen muss.

Folgende Schritte werden von der Klasse initial ausgeführt:

+ WAR File als `java.io.File` definieren (sich selbst finden).  
+ Mit Hilfe von [`ZipInputStream`](http://docs.oracle.com/javase/7/docs/api/java/util/zip/ZipInputStream.html) und [`ZipEntry`](http://docs.oracle.com/javase/7/docs/api/java/util/zip/ZipEntry.html) über alle Dateien des Web Archives iterieren.
+ Für jede Datei innerhalb des *WAR*-Files die entsprechenden Bytes (`byte[]`) ermitteln und zum Namen in einer *Map* ablegen.
+ Handelt es sich bei der Datei um ein *JAR*-File (ORdner *WEB-INF/lib*) muss dieses ebenfalls als *ZipInputStream* behandelt werden und über alle enthaltene Dateien itereiert werden. 


War-File definieren:

<pre><code class="java">	String warFilePath = WarStarter.class.getResource("WEB-INF").getFile();
	warFilePath = warFilePath.substring(0, warFilePath.lastIndexOf("!"));
	warFilePath = warFilePath.substring("file:".length());
	System.out.println("war file path: " + warFilePath);
	
	File warFile = new File(warFilePath);
</code></pre>

ByteArrays definieren:

<pre><code class="java">	private void init(InputStream warFile) throws IOException {
		long startTime = System.currentTimeMillis();
		resourceStore = new HashMap<>();
        ZipInputStream zis = new ZipInputStream(warFile);
        ZipEntry ze;
        while ((ze = zis.getNextEntry()) != null) {
            if (ze.isDirectory()) continue;

            String resourceName = ze.getName();

            if (resourceStore.containsKey(resourceName)) continue;

            byte[] b = getByteArrayFromZip(zis);
            if (resourceName.startsWith("WEB-INF/classes/") && resourceName.endsWith(".class"))               
                //cut off 'WEB-INF/classes
                resourceStore.put(resourceName.substring("WEB-INF/classes/".length()), b);
            else if (resourceName.startsWith("WEB-INF/lib") && resourceName.endsWith(".jar")) {
                InputStream is = new ByteArrayInputStream(b);
                initJarFile(is);
            }
			else
                resourceStore.put(resourceName, b);
        }
        LOG.log(Level.INFO, "WARClassLoader was initalized in {0}ms.", (System.currentTimeMillis() - startTime));
    }
</code></pre>

Details: [https://github.com/mr678/sc-classloaders/blob/master/src/main/java/WarStarter.java](https://github.com/mr678/sc-classloaders/blob/master/src/main/java/WarStarter.java)

## Zugriff

Ein Classloader arbeitet (grob) nach folgenden Schritte:

+ Wenn eine JVM auf eine Klasse zugreift, ruft sie die Methode [`loadClass`](http://docs.oracle.com/javase/7/docs/api/java/lang/ClassLoader.html#loadClass\(java.lang.String\)) auf. Innerhalb dieser Methode wird der Ladeprozess gesteuert. 
+ Hierbei wird als erstes die Methode [`findLoadedClass`](http://docs.oracle.com/javase/7/docs/api/java/lang/ClassLoader.html#findLoadedClass\(java.lang.String\)) aufgerufen, die überprüft, ob die Klasse bereits geladen wurde. Eine Klasse wird von einer *Clasloader-Hierarchie* immer nur einmal geladen.
+ Ist die Klasse noch nicht geladen worden, wird mit der Methode [`findClass`](http://docs.oracle.com/javase/7/docs/api/java/lang/ClassLoader.html#findClass\(java.lang.String\)) die Klasse gesucht.
+ Diese Methode muss von dem neuem *ByteArray-Classloader* überschrieben werden. In der überschriebenen Methode wird geschaut, ob der angeforderte Klassenname in der zu Beginn initalisierten *Map* als *key* enthalten ist.
+ Ist der Name enthalten, wird das *ByteArray* (`byte[]`) in ein *Class*-Object gewandelt, Hierzu wird die Methode  [`defineClass`](http://docs.oracle.com/javase/7/docs/api/java/lang/ClassLoader.html#defineClass\(java.lang.String,byte[],int,int\)) genutzt.  


Implementierung der `findClass`-Methode:

<pre><code class="java">    @Override
    protected Class<? extends Object> findClass(String name) throws ClassNotFoundException {
        LOG.log(Level.FINEST, "WARClassLoader class request for '{0}'.", name);

        String resourceName = name.replace('.', '/') + ".class";

        byte[] b = resourceStore.get(resourceName);
        if (b == null) throw new ClassNotFoundException(name);


        Class<? extends Object> clazz = defineClass(name, b, 0, b.length);
        return clazz;
    }
</code></pre>

Details: [https://github.com/mr678/sc-classloaders/blob/master/src/main/java/WarStarter.java](https://github.com/mr678/sc-classloaders/blob/master/src/main/java/WarStarter.java)

## Manifest einrichten

Damit der neue erstellte *Classloader-Starter* auch genutzt wird, muss dieser im *MANIFEST* des *WAR*-Files eingetragen werden. Mit maven geht dies ganz einfach:

	<plugin>
    	<groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-war-plugin</artifactId>
        <version>2.1.1</version>
        <configuration>
        	<failOnMissingWebXml>false</failOnMissingWebXml>
			<archive>
	        	<manifest>
	            	<mainClass>WarStarter</mainClass>
	            </manifest>
	            <manifestEntries>
					<WarStarter-Main-Class>com.sourcecoding.blog.app.Main</WarStarter-Main-Class>
				</manifestEntries>
			</archive>                       
		</configuration>
	</plugin>

In diesem Beispiel heißt der entwickelte *Classloader-Starter* `WarStarter`und muss als *\*.class*-File wie oben beschrieben im *root*-Ordner des *WAR*-Files liegen.

Der *WarStarter* liest alle Klassen im *WAR*-File ein und startet anschließend die *main*-Methode der im Manifest mit `WarStarer-Main-Class` definierten Klasse.

Details: [https://github.com/mr678/sc-classloaders/blob/master/src/main/java/WarStarter.java](https://github.com/mr678/sc-classloaders/blob/master/src/main/java/WarStarter.java) und [https://github.com/mr678/blog/blob/master/pom.xml](https://github.com/mr678/blog/blob/master/pom.xml)