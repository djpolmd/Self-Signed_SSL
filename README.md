# Self-Signed_SSL
## Create a Self-Signed SSL Certificate for Apache in Ubuntu 16.04

<h4>
In this guide, we will show you how to set up a self-signed SSL certificate for use with an Apache web server on an Ubuntu 16.04 server.
<br></h4>

<h5>

### Step 1: Create the SSL Certificate


`Sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 `<br>
`-keyout /etc/ssl/private/apache-selfsigned.key `<br>
`-out /etc/ssl/certs/apache-selfsigned.crt`<br>
<br>

-out: This tells OpenSSL where to place the certificate that we are creating.

Output<br>
Country Name (2 letter code) [AU]:US<br>
State or Province Name (full name) [Some-State]:New York<br>
Locality Name (eg, city) []:New York City<br>
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Bouncy Castles, Inc.<br>
Organizational Unit Name (eg, section) []:Ministry of Water Slides<br>
Common Name (e.g. server FQDN or YOUR name) []:server_IP_address<br>
Email Address []:admin@your_domain.com<br>
<br>


`sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048`
<br>
<h5><br>
Is may take a few minutes, but when it's done you will have a strong DH group at /etc/ssl/certs/dhparam.pem that we can use in our configuration.
<br>

### Step 2: Configure Apache to Use SSL

`sudo nano /etc/apache2/conf-available/ssl-params.conf`

<H6>

`# from https://cipherli.st/`<br>
`# and https://raymii.org/s/tutorials/Strong_SSL_Security_On_Apache2.html`<br>

`SSLCipherSuite EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH`<br>
`SSLProtocol All -SSLv2 -SSLv3`<br>
`SSLHonorCipherOrder On`<br>
<br>
`# Disable preloading HSTS for now.  You can use the commented out header line that includes`<br>
`# the "preload" directive if you understand the implications.`<br>
`#Header always set Strict-Transport-Security "max-age=63072000; includeSubdomains; preload"`<br>
`Header always set Strict-Transport-Security "max-age=63072000; includeSubdomains"`<br>
`Header always set X-Frame-Options DENY`<br>
`Header always set X-Content-Type-Options nosniff`<br><br>
`# Requires Apache >= 2.4`<br>
`SSLCompression off `<br>
`SSLSessionTickets Off`<br>
`SSLUseStapling on `<br>
`SSLStaplingCache "shmcb:logs/stapling-cache(150000)"`<br>

`SSLOpenSSLConfCmd DHParameters "/etc/ssl/certs/dhparam.pem"`<br>
</h6>

### Modify the Default Apache SSL Virtual Host File 

`sudo cp /etc/apache2/sites-available/default-ssl.conf /etc/apache2/sites-available/default-ssl.conf.bak`

`sudo nano /etc/apache2/sites-available/default-ssl.conf`

/etc/apache2/sites-available/default-ssl.conf

`
<IfModule mod_ssl.c>
        <VirtualHost _default_:443>
                ServerAdmin your_email@example.com
                ServerName server_domain_or_IP

                DocumentRoot /var/www/html

                ErrorLog ${APACHE_LOG_DIR}/error.log
                CustomLog ${APACHE_LOG_DIR}/access.log combined

                SSLEngine on

                SSLCertificateFile      /etc/ssl/certs/apache-selfsigned.crt
                SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key

                <FilesMatch "\.(cgi|shtml|phtml|php)$">
                                SSLOptions +StdEnvVars
                </FilesMatch>
                <Directory /usr/lib/cgi-bin>
                                SSLOptions +StdEnvVars
                </Directory>

                BrowserMatch "MSIE [2-6]" \
                               nokeepalive ssl-unclean-shutdown \
                               downgrade-1.0 force-response-1.0

        </VirtualHost>
</IfModule> `

### (Recommended) Modify the Unencrypted Virtual Host File to Redirect to HTTPS (force redirect)

`sudo nano /etc/apache2/sites-available/000-default.conf`
<br>
Output:

```
<VirtualHost *:80>
        . . .

        Redirect "/" "https://your_domain_or_IP/"

        . . .
</VirtualHost>
```
<br>

`sudo ufw app list`
 IF NOT Apache full then :
 
```
sudo ufw allow 'Apache Full'
sudo ufw delete allow 'Apache'
```

### Step 4: Enable the Changes in Apache

```
sudo a2enmod ssl
sudo a2enmod headers

sudo a2ensite default-ssl

```

`sudo a2enconf ssl-params`

`sudo apache2ctl configtest`

IF
Syntax OK ?  then: 
`sudo systemctl restart apache2`

### Step 5: Test Encryption

https://server_domain_or_IP


 
