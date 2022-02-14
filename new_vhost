#!/bin/bash

#Get new domain name

echo Enter the new domain name:

read domain_name

echo Creating vhost foder...

#docroot folder this is not default Apache docroot 

mkdir -p /var/vhosts/$domain_name

#log folder

mkdir -p /var/vhosts/$domain_name/logs

#creating error and access log files within log folder on docroot

touch /var/vhosts/$domain_name/logs/error.log
touch /var/vhosts/$domain_name/logs/access.log

echo Creating vhost conf file...

# Create the vhost configuration file in /etc/httpd/conf.d/

cat > /etc/httpd/conf.d/$domain_name.conf << EOF 
<VirtualHost *:80>
  ServerAdmin root@$domain_name
  DocumentRoot /var/vhosts/$domain_name
  ServerName $domain_name
  ErrorLog /var/vhosts/$domain_name/logs/error.log
  CustomLog /var/vhosts/$domain_name/logs/access.log combined
</VirtualHost>
EOF

echo Creating index.html file...

cat > /var/vhosts/$domain_name/index.html << EOF
<h1>This is $domain_name</h1>
EOF

echo Setting correct permissions...

# Changing SELinux context o docroot folder,log and index.html files to allow httpd process access

semanage fcontext -a -t httpd_sys_content_t "/var/vhosts/$domain_name/"
semanage fcontext -a -t httpd_sys_content_t "/var/vhosts/$domain_name/index.html"
semanage fcontext -a -t httpd_log_t /var/vhosts/$domain_name/logs/error.log
semanage fcontext -a -t httpd_log_t /var/vhosts/$domain_name/logs/access.log

# Applying SELinux context changes

restorecon -R /var/vhosts/$domain_name/
restorecon -R /var/vhosts/$domain_name/index.html
restorecon -R /var/vhosts/$domain_name/logs/error.log
restorecon -R /var/vhosts/$domain_name/logs/access.log

#adjusting hosts file (when resolving name locally)

echo $(hostname -I | awk '{print $1}') $domain_name >> /etc/hosts

echo Restarting Apache service...

systemctl restart httpd

echo Finished! Visit http://$domain_name

