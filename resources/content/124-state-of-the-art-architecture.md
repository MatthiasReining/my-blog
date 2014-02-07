title=Enterprise Architecture - State of the Art 2014 (Part I - Technologies)
subtitle=JavaEE and .NET
created=2014-02-08T01:22:22
tags=Java, JavaEE, Enterprise Architecture, Architecture, Technologies, Technology Stack
language=en

# Enterprise Architecture - State of the Art 2014

This blog entry gives an answer to what is a state of the art architecture for enterprise application in 2014. OK, it only my opinion ;-) but nevertheless...  
This blog entry is splitted into different articles. 

## Technologies


First of all: **use standards whenever it's possible!**
  
Why? You develop enterprise applications, this means your application runs for many years. For enterprise applications it's very important that the application is maintainable. Therefore it should be possible, that a developer is able to learn the used technology also in the future. For example: in the insurance industry, it's not seldom that an application runs for over 10 years and it's constantly under maintenance.

So, there are more or less only two technology stacks present which provides standards:

* .NET
* JavaEE

### .NET

Although .NET is proprietary language from Microsoft, it is a defacto standard. It is possible technology for an enterprise architecture, because of the market power and propagation of Microsoft software.

But if we talk about standards .NET makes something wrong in the past. OK, .NET makes more wrong than Java ;-)  
Microsoft changed to often there strategy for their .NET framework. The used and recommended frameworks/ technologies changed massively between .NET 3.5 and .NET 4.0.  

### JavaEE

JavaEE is a set of specifications inside the Java world. The Java Enterprise Edition (JavaEE) a real standard. This standard is in the meanwhile backwards compatible for more than 15 years.  
OK, there was also a lot of clumsy stuff like EJB 1/2 in the past.

But nevertheless, the current version 7 of JavaEE is very lightweight and good designed. It's very developer friendly and you can be very productive with this technology!

And it's a standard. **JavaEE is even a real industry standard**.  
The JCP (Java Community Process) defines how this standard look like.  

### other frameworks and languages

Some developers also like the Spring framework (nowadays it becomes less and less people). But the Spring framework is not a standard. It's proprietary framework from SpringSource a division of VMWare. If VMWare would decide tomorrow to close the development for the Spring framework, maybe you will have no support for your enterprise application some years later.  
Same applies for languages like Scala, Groovy and so on

## Conclusion

Use JavaEE for your business logic. 