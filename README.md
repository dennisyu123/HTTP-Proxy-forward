# HTTP-Proxy-forward using Apache2

## Setup http proxy forward

Install apache2
```
sudo apt-get update
sudo apt-get install apache2
```

Enable site config , SSL & proxy mod 

```
sudo a2enmod rewrite
sudo a2enmod ssl
sudo a2enmod proxy_http
sudo a2ensite vdd.conf
sudo a2ensite default-ssl.conf
```

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

restart apache2
```
sudo /etc/init.d/apache2 restart
```


## Generate ssl cert 

Download certbot
```
wget https://dl.eff.org/certbot-auto && chmod a+x certbot-auto
```
Request SSL

```
sudo -H ./certbot-auto certonly --standalone -d vdd.iotw.fun
```

Agree to the Terms of Service and specify if you would like to share your email address with EFF

```
-------------------------------------------------------------------------------
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
agree in order to register with the ACME server at
https://acme-v01.api.letsencrypt.org/directory
-------------------------------------------------------------------------------
(A)gree/(C)ancel: a

-------------------------------------------------------------------------------
Would you be willing to share your email address with the Electronic Frontier
Foundation, a founding partner of the Let's Encrypt project and the non-profit
organization that develops Certbot? We'd like to send you email about EFF and
our work to encrypt the web, protect its users and defend digital rights.
-------------------------------------------------------------------------------
(Y)es/(N)o: n
```

If it sucess it will reutrn similar message
```
IMPORTANT NOTES:
- Congratulations! Your certificate and chain have been saved at:
/etc/letsencrypt/live/example.com/fullchain.pem
Your key file has been saved at:
/etc/letsencrypt/live/example.com/privkey.pem
Your cert will expire on 2018-05-27. To obtain a new or tweaked
version of this certificate in the future, simply run
letsencrypt-auto again. To non-interactively renew *all* of your
certificates, run "letsencrypt-auto renew"
- If you like Certbot, please consider supporting our work by:

Donating to ISRG / Let's Encrypt: https://letsencrypt.org/donate
Donating to EFF: https://eff.org/donate-le
```


## Apply Auto-renew SSL

create renewssl.sh
```
sudo -H ./certbot-auto certonly --apache --renew-by-default -d vdd.iotw.fun 

sudo /etc/init.d/apache2 restart
```

set it can be executed
```
sudo chmod +x renewssl.sh
```

To open your crontab file, execute the following command:
```
sudo crontab -e
```

add script using nano (base on your renewssl.sh dir, example /home/dennis)
It will execute every week at 2:45 

```
45 2 * * 6 cd /home/dennis/ && ./renewssl.sh
```
