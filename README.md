# Web-and-DNS-on-Apache2
Setting up Wordpress and DNS on Linux.

Requirements

Set up DNS

Install apache2

Install mysql-server

Install PHP

Install wordpress

sudo apt update && sudo apt install Wireshark

<img width="1139" alt="Screenshot 2023-06-25 at 18 05 45" src="https://github.com/Mamiololo01/Web-and-DNS-on-Apache2/assets/67044030/e14b68e5-03ae-41f9-973c-8ba1672b40bc">


Open Wireshark and capture traffic

Filter to see DNS and HTTP traffic

<img width="901" alt="Screenshot 2023-06-25 at 18 14 29" src="https://github.com/Mamiololo01/Web-and-DNS-on-Apache2/assets/67044030/4ea349ba-48b5-4196-97e2-feba6557c44c">

<img width="1013" alt="Screenshot 2023-06-25 at 18 17 20" src="https://github.com/Mamiololo01/Web-and-DNS-on-Apache2/assets/67044030/c1de9a27-d2e2-45dc-8c55-c075a0f0ef33">

Using nslookup, host, or dig will show the DNS details

To check authoritative server details

'dig -t ns [website_name]'


<img width="660" alt="Screenshot 2023-06-25 at 18 18 07" src="https://github.com/Mamiololo01/Web-and-DNS-on-Apache2/assets/67044030/826e7de4-c174-4434-ba30-617d9ef731da">



Set up DNS server using Bind 9

Apt update && apt install bind9 bind9utils bind9-doc

Systemctl status bind9

Set it to use IPV4

vi  /etc/default/named

Under the OPTIONS=“-u bind -4”, save and exit the file

"Systemctl restart bind9"

dig -t a @localhost google.com

To show the main confg file 

"cat /etc/bind/named.conf"

DNS Queries 

1. Recursive: A recursive query is a kind of query, in which the DNS server, that received your query, will do all the job, fetching the answer, and giving it back to you. In the end, you’ll get the final answer. 

2. Iterative: The DNS name server will not go and fetch the complete answer for your query but will give back a referral to other DNS servers, which might have the answer. Now it’s your job to query those servers and find the answer.

DNS Forwarder A forwarder is another DNS server that will be queried recursively by our server. A DNS server, configured to use a forwarder, behaves as follows: 

1. When the DNS server receives a query, it attempts to resolve this query. 

2. If the query cannot be resolved using local data, the DNS server forwards the query recursively to the DNS server that is designated as a forwarder. 
 
3. If the forwarder is not unavailable, the DNS server attempts to resolve the query by itself, using iterative querie

tions 
{directory "/var/cache/bind";       // If there is a firewall between you and nameservers you want    // to talk to, you may need to fix the firewall to allow multiple      // ports to talk.  See http://www.kb.cert.org/vuls/id/800113       // If your ISP provided one or more IP addresses for stable       // nameservers, you probably want to use them as forwarders.        // Uncomment the following block, and insert the addresses replacing       // the all-0's placeholder.       // forwarders {      //   0.0.0.0;      // };       //========================================================================      // If BIND logs error messages about the root key being expired,      // you will need to update your keys.  See https://www.isc.org/bind-keys      //========================================================================     dnssec-validation auto;       listen-on-v6 { any; };      forwarders {          8.8.8.8;          8.8.4.4;     };};
//// Do any local configuration here //  // Consider adding the 1918 zones here, if they are not used in your // organization //include "/etc/bind/zones.rfc1918";  zone "crystalmind.academy" {      type master;     file "/etc/bind/db.crystalmind.academy"; }; 
; BIND reverse data file for empty rfc1918 zone ; ; DO NOT EDIT THIS FILE - it is used for multiple zones. ; Instead, copy it, edit named.conf, and use that copy. ; $TTL  86400 @    IN   SOA  ns1.crystalmind.academy. root.localhost. (                      2         ; Serial                 604800         ; Refresh                  86400         ; Retry                2419200         ; Expire                  86400 )  ; Negative Cache TTL ; @    IN   NS   ns1.crystalmind.academy. ;@   IN   NS   ns2.crystalmind.academy. ns1  IN   A    167.99.135.210 @    IN   MX 10      mail.crystalmind.academy. crystalmind.academy.   IN   A    167.99.135.210 www  IN   A    167.99.135.210 mail  IN   A    167.99.135.210 external   IN   A    91.189.88.181

"dig ns -t ns1 www.cisco.com"

systemctl start bind9

Apache2

Install apache

Ufw status

uff allow ‘Apache full’

Document root ls /var/www/html

Echo “Hello” > /var/www/html/me.txt

To confirm check apachewebaddrees/me.txt

Mkdir  /var/www/webaddrees

Ps -ef | grep apache2

Chown -R www-data.www-data /var/www/webaddress for full access

Chmod 755 /var/www/webadress/

Vim /var/www/address/index.html  and add the site content to the file

Cd /etc/apache2

ls

vim sites-available/web address.conf and add the config below;

<VirtualHost *:80>      ServerName crystalmind.academy      ServerAlias www.crystalmind.academy      DocumentRoot /var/www/crystalmind.academy       ServerAdmin webmaster@crystalmind.academy      ErrorLog /var/log/apache2/crystalmind_academy_error.log      CustomLog /var/log/apache2/crystalmind_academy_access.log combined   RewriteEngine on RewriteCond %{SERVER_NAME} =crystalmind.academy [OR] RewriteCond %{SERVER_NAME} =www.crystalmind.academy RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent] </VirtualHost>  

A2ensite webadress and reload the server

<IfModule mod_ssl.c> <VirtualHost *:443>      ServerName crystalmind.academy      ServerAlias www.crystalmind.academy      DocumentRoot /var/www/crystalmind.academy       ServerAdmin webmaster@crystalmind.academy      ErrorLog /var/log/apache2/crystalmind_academy_error.log      CustomLog /var/log/apache2/crystalmind_academy_access.log combined    SSLCertificateFile /etc/letsencrypt/live/crystalmind.academy/fullchain.pem SSLCertificateKeyFile /etc/letsencrypt/live/crystalmind.academy/privkey.pem Include /etc/letsencrypt/options-ssl-apache.conf </VirtualHost> </IfModule>  

Under apache2 dir

Secure Apache with OpenSSL  and Digital Certificate

Apt update && apt install certbot python3-certbot-apache

Certbot -d webadress and will ask

Email address

Agree terms  of service

Share the email address

Generate certificate

Http to https re-direct, yes

Access control by source IP address

Mkdir /var/www/webaddress/admin

Echo “Top secret information” > /var/www/webadress/admin/index.html

Vim /etc/apache2/sites-available/crystalmind.academy-le-ssl.conf

Add the line to the file before the virtual host syntax

<Directory /var/www/webadress/admin>
              Require all denied
              Require ip 6.7.73.2
</Drectory>

You can also do same for file

<Files “report.txt”>
        Require all denied
        Require ip 6.7.73.2
</Files>

To see public Ip add “curl ident.me” command 

.htaccess file

Mkdir  /var/www/web-address/document

Vim /var/www/web-address/documnet/.htaccess

<Files “*.conf”>
        Require all denied
        Require ip public address
</Files>

Creates some files on the dir

Ps -ef > /var/www/webadrees/documents/a.txt

Ps -ef > /var/www/webadrees/documents/b.conf

Ps -ef > /var/www/webadrees/documents/c.log

HTTP Authentication

We have Basic and Digest


Apt install apache2-utils

Htdigest -c /etc/apache2/.htpasswd my server Johnny

Etc the conf

Vim /etc/apache2/sites-available/crystalmind.academy-le-ssl.conf

AuthType Digest

AuthName “Myserver”

AuthDigestProvider file

AuthUserFile /etc/apache2/ .htpasswd

Require valid-user

Save and exit

A2enmod auth_digest and restart the server

Check and validate

Options Directive and indexing in Apache

HTTP comprehension in Apache

DeflateCompressionLevel 7

Monitoring web server

SetHandler and Server Status

E2emod status

/var/www/webadress vim /etc/apache2/mods-available/s

/var/www/webadress vim /etc/apache2/mods-available/status.conf

<Location /Server-status>
               SetHandler server-status
               Require local
               #Require ip 192.0.2.0/24
</Location>

Installing PHP for server side scripting

Apt update && apt install php php-mysql libapache2-mod-php

Enables apache to handle php files

Systemctl restart apache2

Php -v

Vim /etc/var/www/webadress/test.php

Write a php code and check on the browser www.webadress/test/php

Installing Mysql

Apt update && apt install mysql-server

Systemctl status mysql

Secure the mysql

mysql_secure_installation

Ask for password validate YES

Password level 3

Set password for mysql

Remove animus users YES

Restrict root user

Dropping test database YES

Reloading the privilege on test database YES

login

Mysql -u root 

mysql>

Install PhpMyAdmin

Handles admin of SQL

Apt update && apt install myth-admin php-mbstring php-zip php-gd php-json php-curl

Select apache2 and ok

Dbconfig-common YES

SET PASSWORD

Phpenmod mastering and restart the server


Login to mysql server

mysql> CREATE USER ’toor’@‘localhost IDENTIFIED WITH caching_sha2_password BY ‘password123=’;

Installing phpMyAdmin 

Disabling MySql Validate Password Plugin

mysql>UNINSTALL COMPONENT "file://component_validate_password";

mysql>exit;
 		 
Installing phpMyAdmin
sudo apt update && apt install phpmyadmin php-mbstring php-zip php-gd php-json php-curl
 		 
Creating a MySql dedicated user for phpmyadmin

mysql>CREATE USER 'toor'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'toorpass123=';

or using mysql_native_password (backward compatible)

mysql>CREATE USER 'toor1'@'localhost' IDENTIFIED WITH mysql_native_password BY 'toorpass123=';

mysql>GRANT ALL PRIVILEGES ON *.* TO 'toor'@'localhost' WITH GRANT OPTION;
 		 

phpMyAdmin is accessed at https://server_ip_or_domain/phpmyadmin
 		 
Any change made to phpMyAdmin will be done by editing /etc/phpmyadmin/apache.conf
 		 
Change the phpMyAdmin URL:

a) Open /etc/phpmyadmin/apache.conf

b) Change the Alias directive:

Alias /NEW_NAME /usr/share/phpmyadmin

It will be accessed at https://server_ip_or_domain/NEW_NAME
 		 
To limit access to phpMyAdmin by source IP (host) or to add HTTP Authentication add the

Apache directives that protect /usr/share/phpmyadmin in

a <Directory> ... </Directory> section (in /etc/phpmyadmin/apache2) or in .htaccess (in /usr/share/phpmyadmin)

Installing wordpress

Login to mysql server

mysql>  CREATE DATABASE wordpress;

mysql> CREATE USER ’wordpressuser’@‘localhost IDENTIFIED BY ‘password123=’;

mysql>GRANT ALL PRIVILEGES ON wordpress.* TO ‘wordpressuser’@‘localhost' ;

FLUSH PRIVILEGES

EXIT

Downnalod wordpress 

Cd /tmp/

Wget https://wordpress.org/latest.tar.gz

Then extract the archive

Tar -xzvf latest.tar.gz

Then move wordpress to the webpage root dir

ls wordpress

ls -l /var/www/webadresss/

It will show the index.html and test.php. Remove the two files

rm -rf /var/www/webadress/*

mv wordpress/* var/www/webadresss/

Change the ownership

Ps -ef | grep apache2

Chown -R www-data.www-data var/www/webadresss/

Check the wordpress content
ls -l /var/www/webadress/

Go to browser and type www.webadress/wp-admin and follow the set up wizard and fill all parameters	and complete the set up. Login to the dashboard and create your website.

WordPress Security 

Always use the latest version of WordPress and keep all plugins up to date. 

Use only strong passwords (min. 10 random characters including special ones). 

Limit login attempts using a plugin or a WAF. 

Install a security plugin or a WAF (Web Application Firewall). Exemple: WordFence. 

Add 2-step verification (using a security plugin). 

Protect wp-admin directory (source IP access or username and passwords). 

Make backups regularly and test them
