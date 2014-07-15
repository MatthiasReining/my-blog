title=Blogging With Markdown, GitHub, Glassfish and Apache HTTP Server
subtitle=This weblog...
created=2014-07-16T00:38:21
tags=GitHub, Markdown, Blogging, Glassfish, Apache HTTP Server, Java, JavaEE
language=en

This article discusses how I developed this blog.  
The blog is based on [Markdown](http://de.wikipedia.org/wiki/Markdown) articles which are stored at [GitHub](https://github.com/mr678/my-blog).

<img src="https://raw.githubusercontent.com/mr678/my-blog/master/resources/web-resources/img/article-images/my-blog.png" alt="blog roundtrip" width="100%">

The GitHub service hook (Webhook) is used to trigger [my blog app](https://github.com/mr678/blog/blob/master/src/main/java/com/sourcecoding/blog/business/build/boundary/BlogBuilder.java) via RESTful Web Service. [My blog app](https://github.com/mr678/blog) transfers the markdown input to HTML by using [pegdown](https://github.com/sirthias/pegdown). The blog structure is designed by the template engine [freemarker](http://freemarker.org/). The [blog app](https://github.com/mr678/blog) exports static HTML files to the [Apache HTTP Server](https://httpd.apache.org/).
