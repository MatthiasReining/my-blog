title=How you can use a local library with maven without 'mvn:install'
subtitle=It's not a nice way, but it works...
created=2013-05-21T18:13:14
tags=Java, maven, mvn, maven3, install, library, libraries, local libraries
language=en

If you want to use a local library in your maven project without to add this library to your local maven repositry by using `mvn install:install-file`, a possible way for including could be the following:


### Define/create a local library directory structure in a maven way.

	src
		+ local-libs
			+ <groupId>
				+ <artifactId>
					+ <version>
						library_xy-<version>.jar

Example:

	src/local-libs/it/rgi/jbasisconfig/0.0.1/jbasisconfig-0.0.1.jar


### Add local repository to your pom.xml 

	<repositories>        
        <repository>
            <id>ProjectRepo</id>
            <name>ProjectRepo</name>
            <url>file://${project.basedir}/src/local-libs</url>
        </repository>
    </repositories>

The variable `{project.basedir}`is always available and shows to your project root directory. In my case I add a local directory `local-libs` under `src` directory.



### Add your dependency to your local library in normal maven style

	<dependency>
		<groupId>it.rgi</groupId>
		<artifactId>jbasisconfig</artifactId>
		<version>0.0.1</version>            
	</dependency>


That's it. Of course this is not a nice solution but sometimes pragmatic.