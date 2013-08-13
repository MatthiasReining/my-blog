title=Download JDK via wget
subtitle=Accept the Oracle license agreement
created=2013-08-12T23:27:13
tags=Java, JDK, Linux, wget, installation
language=en

When downloading a JRE/JDK you have to accept the "Oracle Binary Code License Agreement". Without accepting the license you're not able to download the package, therefore you cannot directly use `wget`. Maybe this could be a problem if you want to install a JDK on a server environment without X.

With following trick you're able to download the JDK with `wget` anyway.


	wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com" "http://download.oracle.com/otn-pub/java/jdk/7u25-b15/server-jre-7u25-linux-x64.tar.gz"

