title=Testing JSON on JavaEE 7 Application Server -JsonProviderImpl not found
subtitle=ERROR Provider org.glassfish.json.JsonProviderImpl not found
created=2013-09-20T15:42:10
tags=JavaEE 7, JSON, JsonProviderImpl not found, Troubleshooting
language=en

JavaEE 7 provides also a JSON API. That's pretty cool! Now you must not any longer link to an external JSON Provider for your RESTful JavaEE application!

But if you want to test some of your code, where you navigate through the JSON object, maybe you get following error in your jUnit test code:

	Caused an ERROR Provider org.glassfish.json.JsonProviderImpl not found

[when using Glassfish 4 as your application server]

Your application normally has following dependencies:

	<dependency>
		<groupId>javax</groupId>
		<artifactId>javaee-api</artifactId>
		<version>7.0</version>
		<scope>provided</scope>
	</dependency>

	
This means your test code has also access to the javaee-api. But the api defines only interfaces / no implementation. The implementation is part of the application server (in this case Glassfish 4) and this means, that your unit test has no access to the needed classes and you get the `JsonProviderImpl not found` exception.

You can solve this error by adding the implementation to your test dependencies

	<dependency>  
		<groupId>org.glassfish</groupId> 
		<artifactId>javax.json</artifactId> 
		<version>1.0.2</version> 
		<scope>test</scope>
	</dependency>

	