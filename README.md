# HTTP-Proxy-forward

## Setup http proxy forward

Change to apache2 config file
```
cd /etc/apache2/sites-available/
```

Add to following script to vdd.conf

```
#!apache

<VirtualHost *:80>
ServerName vdd.iotw.fun
ServerAlias vdd.iotw.fun

RewriteEngine On
RewriteRule ^ https://vdd.iotw.fun%{REQUEST_URI} [R=301,L]

</VirtualHost>
```

Add the following script to default-ssl.conf

```
#!apache

<VirtualHost *:443>
ServerName vdd.iotw.fun

SSLEngine On
SSLCertificateFile "/etc/letsencrypt/live/vdd.iotw.fun/cert.pem"
SSLCertificateKeyFile "/etc/letsencrypt/live/vdd.iotw.fun/privkey.pem"
SSLCertificateChainFile "/etc/letsencrypt/live/vdd.iotw.fun/chain.pem"

ProxyPass / http://0.0.0.0:3000/
ProxyPassReverse / http://0.0.0.0:3000/

</VirtualHost>
```
## Generate ssl cert 

Please read [how to](https://www.digitalocean.com/community/tutorials/how-to-use-certbot-standalone-mode-to-retrieve-let-s-encrypt-ssl-certificates-on-ubuntu-16-04)

## Create renewssl.sh

nano renewssl.sh
```
sudo -H ./certbot-auto certonly --apache --renew-by-default -d vdd.iotw.fun 

sudo /etc/init.d/apache2 restart
```

sudo chmod +x remewssl

restart apache2
```
./renewssl.sh
```
