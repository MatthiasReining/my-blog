title=Import a static web project from Eclipse into Netbeans
subtitle=
created=2014-05-12T15:21:44
tags=Netbeans, Eclipse, import, static web project  
language=en

In have to work in an Eclipse based project environment. But for many tasks I prefer to work with Netbeans. To work with *Netbeans* for *Eclipse* projects is normally very easy. You can use in Netbeans following commands: 

File -> Import Project -> Import Eclipse Project -> Select the workspace -> choose the project you want to work with -> done.

The project structure is not changed, what allows you to switch comfortable between both IDEs.

Unfortunately, I was not able to import a *static web project* into Netbeans :-(  
Here you can find my workaround to let it work...

Instead of import the project I directly open the project in Netbeans (File -> Open Project).

To let these work following files have to be manually added:

	<eclipse-workspace>/<project-folder>/nbproject
	<eclipse-workspace>/<project-folder>/nbproject/project.properties
	<eclipse-workspace>/<project-folder>/nbproject/project.xml


Content of **project.xml**:

	<?xml version="1.0" encoding="UTF-8"?>
	<project xmlns="http://www.netbeans.org/ns/project/1">
	    <type>org.netbeans.modules.web.clientproject</type>
	    <configuration>
	        <data xmlns="http://www.netbeans.org/ns/clientside-project/1">
	            <name>*eclipseProjectName*</name>
	        </data>
	    </configuration>
	</project>

Content of **project.properties**:

	config.folder=
	file.reference.eclipseProjectName-app=app
	file.reference.eclipseProjectName-config=config
	file.reference.eclipseProjectName-public_html=WebContent
	file.reference.eclipseProjectName-test=test
	files.encoding=UTF-8
	site.root.folder=${file.reference.eclipseProjectName-app}
	test.folder=


You have to replace **\*eclipseProjectName\*** with your project name!

After these it should be possible to open directly a eclipse based static web project into Netbeans!