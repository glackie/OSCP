When an SSL/TLS (Secure Sockets Layer/Transport Layer Security) certificate is created for a domain by a CA (Certificate Authority), CA's take part in what's called "Certificate Transparency (CT) logs". 

These are publicly accessible logs of every SSL/TLS certificate created for a domain name.

The purpose of Certificate Transparency logs is to stop malicious and accidentally made certificates from being used. 

We can use this service to our advantage to discover subdomains belonging to a domain, sites like [https://crt.sh](http://crt.sh) and [https://ui.ctsearch.entrust.com/ui/ctsearchui](https://ui.ctsearch.entrust.com/ui/ctsearchui) offer a searchable database of certificates that shows current and historical results.