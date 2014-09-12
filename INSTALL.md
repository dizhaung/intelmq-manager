
This document assumes you install on a Debian or Ubuntu system.

# Installing intelmq-controller on a webserver

## lighttpd

First make sure that you have the latest version of  intelmq-control-platform.
Then install lighttpd:

```
apt-get install lighttpd
```

### PHP

Next, install php in fast-cgi mode:
(taken from the manual at: http://www.betamaster.us/blog/?p=911)

```
apt-get install php5-cgi
```

Enable php5 fastcgi:

```
/usr/sbin/lighty-enable-mod fastcgi
/usr/sbin/lighty-enable-mod fastcgi-php
service lighttpd restart
```

On Debian jessie, the config file for fastcgi on lighttpd looks like this:

```
cat /etc/lighttpd/conf-enabled/15-fastcgi-php.conf
# -*- depends: fastcgi -*-
# /usr/share/doc/lighttpd/fastcgi.txt.gz
# http://redmine.lighttpd.net/projects/lighttpd/wiki/Docs:ConfigurationOptions#mod_fastcgi-fastcgi

## Start an FastCGI server for php (needs the php5-cgi package)
fastcgi.server += ( ".php" =>
	((
		"bin-path" => "/usr/bin/php-cgi",
		#"bin-path" => "/usr/sbin/php5-fpm",
		"socket" => "/var/run/lighttpd/php.socket",
		#"socket" => "/run/php5-fpm.sock",
		"max-procs" => 1,
		"bin-environment" => (
			"PHP_FCGI_CHILDREN" => "4",
			"PHP_FCGI_MAX_REQUESTS" => "10000"
		),
		"bin-copy-environment" => (
			"PATH", "SHELL", "USER"
		),
		"broken-scriptfilename" => "enable"
	))
)
```

### basic authentication


## Apache

Start by installing Apache and PHP

```
sudo apt-get install apache2

sudo apt-get install php5

sudo apt-get install libapache2-mod-php5
```

After Apache and PHP are installed get the latest version of IntelMQ Manager and copy it's contents to your chosen directory. Configure Apache accordingly and make sure to change php/config.php to put the correct command and the correct paths for configuration files. Also, don't forget to give read/write access to the following folders:

```
/var/log/intelmq/
/var/run/intelmq/
/var/lib/intelmq/
```

### Basic Authentication

If you want to enable basic authentication on IntelMQ Manager edit the httpd.conf and insert 

```
    AuthType basic 
    AuthName <realm name>

    AuthBasicProvider file
    AuthUserFile <password file path>
```

Where <realm name> is the string that identifies the realm that should be used and <password file path> is the path to the file created with the htpasswd command.

To create a new file do:

```
    htpasswd -c <password file path> <username>
```

To edit an existing one do:

```
    htpasswd <password file path> <username>
```