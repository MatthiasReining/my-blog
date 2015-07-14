title=7-8-9 | CentOS 7, Java 8, Wildfly 9
subtitle=Installing Wildfly 9 on CentOS 8
created=2015-07-15T01:24:24
tags=Wildfly9, CentOS 7, Java 8, Installation
language=en

I just installed a new VM with CentOS 7, Java 8 and Wildfly 9.

Here you can find my installation path:

# Install CentOS 7

I use a '**CentOS 7 minimal**' *ready to use* image on my VM from Server4you.

Thanks to the Server4you infrastructure, 2 minutes later the server was up and running.

# Installing JDK 8 on CentOS 7

Here you find a shell commands to install JDK 8 for 64bit provided by Oracle:

	[root@euve77943 ~]# cd /opt
	[root@euve77943 opt]# wget --no-cookies --no-check-certificate \
		--header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" \
		"http://download.oracle.com/otn-pub/java/jdk/8u51-b16/jdk-8u51-linux-x64.tar.gz"
	[root@euve77943 opt]# tar xzf jdk-8u51-linux-x64.tar.gz
	[root@euve77943 opt]# rm -f jdk-8u51-linux-x64.tar.gz

Alternative you can also a CentOS package:

	[root@euve77943 ~]# yum install java-1.8.0-openjdk.x86_64

I personal prefer the first way. Using this way you have no delay to install the latest version.

## Configure the Java alternatives

	[root@euve77943 opt]# cd jdk1.8.0_51/
	[root@euve77943 jdk1.8.0_51]# alternatives --install /usr/bin/java  java /opt/jdk1.8.0_51/bin/java 2

	[root@euve77943 jdk1.8.0_51]# alternatives --config java
	
	There are 2 programs which provide 'java'.
	
	  Selection    Command
	-----------------------------------------------
	*+ 1           /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.45-30.b13.el7_1.x86_64/jre/bin/java
   	2           /opt/jdk1.8.0_51/bin/java
	
	Enter to keep the current selection[+], or type selection number: 2

# Installing Widlfly 9 on CentOS 7

Thanks to Dmitriy Sukharev! (Great script!)

[https://gist.github.com/sukharevd/6087988](https://gist.github.com/sukharevd/6087988)

	[root@euve77943 ~]# cd /
	[root@euve77943 /]# mkdir data
	[root@euve77943 /]# cd data/
	[root@euve77943 data]# wget https://gist.githubusercontent.com/sukharevd/6087988/raw/85ec5695c9fa52686764a976d19a9dabeb4ed306/wildfly-install.sh

	[root@euve77943 data]# mkdir wildfly
	[root@euve77943 data]# /bin/bash wildfly-install.sh

If using the script from Dmitriy Wildfly starts on port 28080. You can configure this in standalone.xml


That's it :-)