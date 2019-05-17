# Vagrant LAMP

This is a Vagrantfile to provision a LAMP stack on Ubuntu 18.04.

This sets up the following applications:

Apache/2.4.29
PHP 7.2.17
MySQL 5.7.26

The Vagrantfile automatically configures MySQL with a root password of root.  This can be changed at the top of the Vagrantfile.

Apache is configured to allow .htaccess override and a .htaccess with a DirectoryIndex of index.php is set.

The html folder is synced to /var/www/html

Internal port 80 is mapped to external port 8080.

Composer is installed and added to /usr/local/bin.

A number of PHP modules are installed in order to meet the Laravel minimum install requirements.

Several tools like curl, git, wget, and zip are installed.
