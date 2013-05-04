title=JAX-RS, JSON and DTOs
subtitle=Don't use DTOs!
created=2013-05-04T11:12:21
tags=Java, JavaEE, REST, JAX-RS, DTOs
language=en

Sometimes it is difficult to use entity-beans directly in your view layer or in your REST interface. There are a lot of blog entries and discussions about this topic. For example:

[http://www.adam-bien.com/roller/abien/entry/value_object_vs_data_transfer](http://www.adam-bien.com/roller/abien/entry/value_object_vs_data_transfer)  
[http://www.adam-bien.com/roller/abien/entry/how_evil_are_actually_data](http://www.adam-bien.com/roller/abien/entry/how_evil_are_actually_data)  
Both are from Adam Bien's blog with very interesting comments.

Now some explanation how I deal with DTOs in REST interfaces (specially for **HTML5/REST/JSON** applications):

It is easy to use entity-beans directly in your REST interface:

* You save a lot of code.
* You implement the DRY principle and... 
* You often get a lot of problems ;-)

Some of the common problems are:

* lazy loading exceptions
* different need
	* data for a list with some special information from other entity beans
	* data for an object browser (here are entity beans possible)
	* > sometimes you want to use `@JsonIgnore`/`@XMLTransparent`, sometimes you don't.
* very complex back-end models with a lot of relations.
* back-end models with cycles.

**Now a suggestion could be to introduce DTOs to solve this listed problems.  
But I think this is not a very nice solution!**

My recommendation:

**For REST interfaces use `Map<String, Object>` as transfer object!**

Object can be: `String`, `Long`, `Double` or `List<Object>`

Why?

* In the most cases the client side uses a different format between receiving and sending data. So the structure you send from the server to the client is  not recycled. You can build a nested Map instead of using setter of a normal DTO.
* The mapping between a DTO (normal Java object) and *clean* JSON structure is often a nightmare.
* The structure `Map<String, Object>` and `List<Object>` (inside your Map) fits perfect to the JavaScript/JSON side.  