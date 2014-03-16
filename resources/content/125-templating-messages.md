title=Java Template Engine - The Easy One
subtitle=Use MessageFormat as easy template engine.
created=2014-03-16T00:15:14
tags=Java, MessageFormat
language=en

Searching for an easy templating engine, without much dependencies, I thought about Freemarker, Velocity and so on. But I have only a need for some small templates for emails. Therefore these kind of templating engines was to heavy weight for me...

Thinking about: the *old* **MessageFormat** from JavaSE is the best solution for me :-) 

[http://docs.oracle.com/javase/7/docs/api/java/text/MessageFormat.html](http://docs.oracle.com/javase/7/docs/api/java/text/MessageFormat.html)

	int planet = 7;
	String event = "a disturbance in the Force";
	
	 String result = MessageFormat.format(
	     "At {1,time} on {1,date}, there was {2} on planet {0,number,integer}.",
	     planet, new Date(), event);

The output is:

	 At 12:30 PM on Jul 3, 2053, there was a disturbance in the Force on planet 7.

Of Course the placeholder with `{0}`, `{1}` and so on is not really nice, but for simple templating tasks it is perfect!   