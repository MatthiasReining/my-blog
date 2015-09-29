title=Caching as a Standard - Using JSR 107 on an Application Server
subtitle=let's run javax.cache with Infinispan
created=2015-09-30T01:02:12
tags=Infinispan, JSR 107, JCache, Wildfly, Cache
language=en

I like to use a cache in one of my Java EE app! Caching... sounds like a standard task.

Therefore, I just had a look to the Java EE specification. Nothing :-(

Ok. After a short while (and some google results), I have remebered: caching was already a topic for Java EE 7. But in the 
last second it was not excluded - there was no time for it.

But, what's the current sitatuion?

- The JSR 107 for Caching is final  
[http://download.oracle.com/otndocs/jcp/jcache-1_0-fr-eval-spec/index.html](http://download.oracle.com/otndocs/jcp/jcache-1_0-fr-eval-spec/index.html)
- Infinispan has also already an implementation (or better an adapated API)  
[http://infinispan.org/docs/8.0.x/user_guide/user_guide.html#_using_infinispan_as_a_jsr107_jcache_provider](http://infinispan.org/docs/8.0.x/user_guide/user_guide.html#_using_infinispan_as_a_jsr107_jcache_provider)

That's great!

Infinispan has also some examples how to use the cache:

First the mvn dependency:

    <dependency>
        <groupId>org.infinispan</groupId>
        <artifactId>infinispan-jcache</artifactId>
        <version>8.0.1.Final</version>
    </dependency>

But you need a little bit more. The pure API with the interfaces for javax.cache. For this you have to add also the JCache API to your maven pom.xml.

    <dependency>
        <groupId>javax.cache</groupId>
        <artifactId>cache-api</artifactId>
        <version>1.0.0</version>
    </dependency>

Now you can go ahead with all the examples and use this fantastic caching API like described here: [http://infinispan.org/docs/8.0.x/user_guide/user_guide.html#_create_a_local_cache](http://infinispan.org/docs/8.0.x/user_guide/user_guide.html#_create_a_local_cache).

If Java EE 8 will become reality on your app server, it should be possible to remove these dependencies from your maven pom.xml. 
Than the implementation is out-of-the box available and shipped with your favourite application server.
