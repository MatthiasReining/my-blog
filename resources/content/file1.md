title=Multitenancy 4 Java EE
subtitle=Tenants on an AppServer
created=2013-01-22T18:14:00
tags=Java EE, Multitenancy, JBoss AS 7

1. Download
===========

Maven Users
-----------

If you are using Maven, simply copy the following dependency into your pom.xml file.
The artifact is hosted at [Maven Central Repository](http://search.maven.org/#search%7Cga%7C1%7Ccom.sourcecoding.multitenancy).

	<dependency>
	    <groupId>com.sourcecoding</groupId>
	    <artifactId>multitenancy</artifactId>
	    <version>0.3.2</version>
	</dependency>

JBoss AS 7 (picketbox) is an dependency of multitenancy. Unfortunately picketpox is not in maven central repository, so you have
to add the JBoss repository in your pom.xml

	<repository>
		<id>jboss-releases-repository</id>
		<name>JBoss Releases Repository</name>
		<url>http://repository.jboss.org/nexus/content/groups/public-jboss/</url>
	</repository>


Everyone else
-------------

Start using maven ;-)

But you can also easily download the jar File direct
from [Maven Central Repository](http://search.maven.org/#browse%7C-913672894)
and put it your WEB-INF\lib Folder. There's no *direct* dependencies if you use
multitenancy4jee with JBoss AS 7.

You will find multitenancy4jee as source, javadoc and jar-package.





<a name="multitenancy-authentication"/>
2. Multitenancy Authentication
==============================

Die multitenancy4jee Bibliothek erm�glicht die Authentifizierung eines Benutzer
mit Benutzernamen, Passworrt und Mandant.

Authentication Concept
----------------------

Dies wird erm�glicht durch die Verwendung des `MultiTenancyPrincipal`. Dieser
basiert und erweitert die Klasse `org.jboss.security.SimplePrincipal` um ein
Mandanten Attribut (tenant).

Aktuell wird lediglich eine datenbankbasierte Benutzerverwaltung unterst�zt.
Hierzu muss eine JPA User-Entit�t das Interface `MultiTenancyUser` implementieren
und eine JPA Role-Entit�t das Interface `MultiTenancyUserRole`.

Um die Verwendung zu vereinfachen existieren hierzu bereits die abstrakten
JPA Entit�ten `AbstractMultiTenancyUser` und `AbstractMultiTenancyUserRole`
(jeweils gekennzeichnet mit `@MappedSuperclass`).


<a name="multitenancy-authentication-entity"/>
Authentication Entity
---------------------

Die multitenancy4jee Bibliothek definiert nicht direkt die Datenbankentit�ten.
Damit ist es m�glich die Entit�ten selbst zu definieren inklusive weiterer
zus�tzlicher Attribute wie bspw. created, lastLogin, loginFailed usw.

Folgende Beispiele zeigt, was notwendig ist um die beiden Enti�ten zu definieren:



Beispielentit�t **User**

	package de.mreining.example.model;

	import java.util.List;

	import javax.persistence.CascadeType;
	import javax.persistence.Entity;
	import javax.persistence.GeneratedValue;
	import javax.persistence.Id;
	import javax.persistence.OneToMany;

	import com.sourcecoding.multitenancy.AbstractMultiTenancyUser;
	import com.sourcecoding.multitenancy.MultiTenancyUser;
	import com.sourcecoding.multitenancy.MultiTenancyUserRole;

	@Entity
	public class MyUser extends AbstractMultiTenancyUser implements MultiTenancyUser {

		private static final long serialVersionUID = 1L;

		@Id
		@GeneratedValue
		private Long id;

		@OneToMany(mappedBy = "multiTenancyUser", cascade = CascadeType.ALL)
		private List<MyUserRole> roles;

		public Long getId() {
			return id;
		}

		public void setId(Long id) {
			this.id = id;
		}

		@Override
		public List<MyUserRole> getRoles() {
			return roles;
		}

		@SuppressWarnings("unchecked")
		@Override
		public void setRoles(List<? extends MultiTenancyUserRole> roles) {
			this.roles = (List<MyUserRole>) roles;

		}
	}


Beispielentit�t **UserRole**

	package de.mreining.example.model;

	import javax.persistence.Entity;
	import javax.persistence.GeneratedValue;
	import javax.persistence.Id;
	import javax.persistence.ManyToOne;

	import com.sourcecoding.multitenancy.AbstractMultiTenancyUserRole;
	import com.sourcecoding.multitenancy.MultiTenancyUser;

	@Entity
	public class MyUserRole extends AbstractMultiTenancyUserRole {

		private static final long serialVersionUID = 1L;

		@Id
		@GeneratedValue
		private Long id;

		@ManyToOne
		private MyUser myTenancyUser;

		public Long getId() {
			return id;
		}

		public void setId(Long id) {
			this.id = id;
		}

		@SuppressWarnings("unchecked")
		@Override
		public <T extends MultiTenancyUser> T getMultiTenancyUser() {
			return (T) multiTenancyUser;
		}

		@Override
		public <T extends MultiTenancyUser> void setMultiTenancyUser(T multiTenancyUser) {
			this.multiTenancyUser = (ItomUser) multiTenancyUser;
		}
	}

