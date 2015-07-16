title=Rewriting Subdomains to Path
subtitle=mod_rewrite with Apache HTTP server
created=2015-07-17T00:45:24
tags=Apache HTTP server, mod_rewrite, subdomains
language=en

Following scripts helps me to rewrite a subdomain to a URL path.

    RewriteCond %{HTTP_HOST} ^([^\.]+)\.(.*)
    RewriteRule ^(.*) %{REQUEST_SCHEME}://%2/%1

For example, this transforms the URL
`http://ci.mydomain.com` to `http://mydomain.com/ci/`.

The good thing, the domain and the subdomain is not hard coded. Therefore, you can use this pattern flexible for generic hosts.