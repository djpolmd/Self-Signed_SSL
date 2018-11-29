# Self-Signed_SSL
Create a Self-Signed SSL Certificate for Apache in Ubuntu 16.04

<h4>
In this guide, we will show you how to set up a self-signed SSL certificate for use with an Apache web server on an Ubuntu 16.04 server.
<br></h4>

<h5>

<B>Step 1: Create the SSL Certificate<B><br>

<h6>
Sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt

</h6>
-out: This tells OpenSSL where to place the certificate that we are creating.

Output
Country Name (2 letter code) [AU]:US
State or Province Name (full name) [Some-State]:New York
Locality Name (eg, city) []:New York City
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Bouncy Castles, Inc.
Organizational Unit Name (eg, section) []:Ministry of Water Slides
Common Name (e.g. server FQDN or YOUR name) []:server_IP_address
Email Address []:admin@your_domain.com
