# SSL-install-on-apache-server-in-AWS
--------------------------------------

# Install SSL on AWS Apache server Ubuntu 14.04.6 LTS Server


1.Check if your AWS instance has OpenSSL installed

    sudo dpkg –s openssl
 
 The results will show the following:
        
    Package: openssl
    Status: install ok installed
    Version: 1.0.1f-1ubuntu2.19
   
2.Activate the SSL Module
   
   To enable the module
   
    sudo a2enmod ssl
   
    sudo service apache2 restart
   
3.Generate a Certificate Signing Request (CSR) for your server and Private Key

   First, create a subdirectory in which you will place the private key and the CSR file. Run the following command:
   
    sudo mkdir /etc/apache2/ssl
   
#   if don't have certificate file:
   
   run the following command to generate a private key and a public Certificate Signing Request (CSR) for the webserver:
   
    openssl req -nodes -newkey rsa:2048 -keyout myserver.key -out server.csr
    
   Please note: for ease of use, it is recommended that you replace ‘server’ with the domain name the certificate will be issued for and similarly do that for the             ‘myserver’ in the key file as well.

   Now enter the details for your CSR:

       Country Name (2 letter code) [AU]: GB
       State or Province Name (full name) [Some-State]: Surrey
       Locality Name (eg, city) []: London
       Organization Name (eg, company) [Internet Widgits Pty Ltd]: Company Name Pvt Ltd
       Organizational Unit Name (eg, section) []: IT Department
       Common Name (eg, YOUR name) []: yourdomain.com
       Email Address []:webmaster@yourdomain.com
       A challenge password []:
       An optional company name []:
       
   The key and certificate will be created and placed in your /etc/apache2/ssl directory. The fields email address, optional company name and challenge password can be left blank. If you enter ‘.’, the field will be left blank.
   
#   If already have a Certificate
   
   Uplaod certificate to /etc/apache2/ssl directory.
  
4.Configure Apache to use SSL

    sudo nano /etc/apache2/sites-available/default-ssl.conf
    
Without the comments, the file looks like this:

    <IfModule mod_ssl.c>
    <VirtualHost _default_:443>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/ssl-cert-snakeoil.pem
    SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
    <FilesMatch “\.(cgi|shtml|phtml|php)$”>
    SSLOptions +StdEnvVars        </FilesMatch>
    <Directory /usr/lib/cgi-bin>
    SSLOptions +StdEnvVars
    </Directory>
    BrowserMatch “MSIE [2-6]”
    \ nokeepalive ssl-unclean-shutdown
    \ downgrade-1.0 force-response-1.0
    BrowserMatch “MSIE [17-9]” ssl-unclean-shutdown
    </VirtualHost>
    </IfModule>

We will configure for a virtual host (ServerAdmin, ServerName, ServerAlias, DocumentRoot, etc.) as well as change the location of where Apache looks for the SSL certificate and key.

Add the following item in the file right after “ServerAdmin”:

ServerName yourdomain.com
Change only the following items in the file:

    ServerAdmin webmaster@localhost
    ServerAlias yourdomain.com
    DocumentRoot /var/www/html/
    SSLEngine on
    SSLCertificateFile /etc/apache2/ssl/your_certificate.crt
    SSLCertificateKeyFile /etc/apache2/ssl/your_private.key
    SSLCertificateChainFile /etc/apache2/ssl/your_ca_bundle.crt
    
Press Control + X and then select ‘Y’ to save your changes.

5.Activate the SSL Virtual Host

   we have configured the SSL-enabled virtual host, we need to enable it

    sudo a2ensite default-ssl.conf
    
   You will need to restart the server for the changes to come into effect. Run the following command:
   
    sudo service apache2 restart
   

6. redirect http to https redirect 
    
        sudo cd /var/www/html/
        sudo nano .htaccess
  
write .htaccess

      <IfModule mod_rewrite.c>
      RewriteEngine On
      RewriteCond %{SERVER_PORT} !^443$
      RewriteRule (.*) https://www.yourdomainame.com/$1 [R]
      </IfModule>
      
after ctl+x and type "Y" 

Now restart the server

    sudo service apache2 restart
    
check your site...


ref: https://financetrainingcourse.com/education/2016/06/ssl-certificate-install-aws-guide-apache-ubuntu-14-04/


