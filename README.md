# JTrack-Backend
This repository provides instructions and required commands to set up your own server instance for conducting studies using mobile devices.

### Before you begin
1. Register a domain for your backend service

2. Set up SSL for your domain to use https

3. Configure your webserver
* Create a new config file (e.g. in apache2)

`vi /etc/apache2/sites-available/010-mydomain.com-le-ssl.conf`
* define a domain endpoint for handling requests by mobile devices

`WSGIScriptAlias /api /var/www/mydomain.com/service/jutrack_service.wsgi`
* define a domain endpoint for transferring non-text files between server and device

`WSGIScriptAlias /resources /var/www/mydomain.com/service/jutrack_fetch_resources.wsgi`
* define a domain endpoint for the dashboard

`WSGIScriptAlias / /var/www/mydomain.com/www/dashboard/jutrack_dashboard.wsgi`

The finished configuration file could look like this:
```
<VirtualHost *:80>
	ServerAdmin dummymail@my-institution.com
	ServerName mydomain.com

    Redirect temp / https://mydomain.com/
    
    ServerSignature Off
</VirtualHost>

<IfModule mod_ssl.c>
<VirtualHost *:443>
    ServerAdmin dummymail@my-institution.com
    ServerName mydomain.com

    DocumentRoot /srv/mydomain.com/dashboard

    <Directory /srv/mydomain.com/dashboard>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride None
        Require all granted
    </Directory>
    

    WSGIScriptAlias /api /var/www/mydomain.com/service/jutrack_service.wsgi
    WSGIScriptAlias /resources /var/www/mydomain.com/service/jutrack_fetch_resources.wsgi
    # dashboard
    WSGIScriptAlias / /var/www/mydomain.com/www/dashboard/jutrack_dashboard.wsgi
    WSGIDaemonProcess my_dashboard user=www-data group=my_group threads=5 home=/var/www/mydomain.com/www/dashboard
    WSGIProcessGroup my_dashboard

    #set uploads to unlimited (though might be capped at 2GB)
    LimitRequestBody 0

    # Possible values include: debug, info, notice, warn, error, crit, alert, emerg.
    LogLevel warn

    ErrorLog ${APACHE_LOG_DIR}/mydomain.com.error.log
    CustomLog ${APACHE_LOG_DIR}/mydomain.com.access.log combined

    ServerSignature Off

    SSLCertificateFile /etc/letsencrypt/live/mydomain.com/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/mydomain.com/privkey.pem
    Include /etc/letsencrypt/options-ssl-apache.conf
</VirtualHost>
</IfModule>
```
4. Discuss required user/service permissions with your system admin

### Setting up service scripts

### Setting up the dashboard
