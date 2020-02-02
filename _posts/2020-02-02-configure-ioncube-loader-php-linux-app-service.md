---
layout: post
title: Configure IonCube Loader - PHP Linux App Service
date: 2020-02-02 21:32 +0530
---

### Steps:

- Download the ioncube library and extract it under /home/site directory:

```sh
wget http://downloads3.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz
tar xvfz ioncube_loaders_lin_x86-64.tar.gz
```

- Create a php.ini file under /home/site directory with the following content:  

```ini
zend_extension=/home/site/ioncube/ioncube_loader_lin_7.2.so  
error_log=/dev/stderr  
display_errors=Off  
log_errors=On  
display_startup_errors=Off  
date.timezone=UTC  
zend_extension=opcache  
```

- Create a startup script to copy this php.ini to /usr/local/etc/php/

__Example startup.sh:__

```sh
cp /home/site/php.ini /usr/local/etc/php/php.ini
rm /usr/local/etc/php/conf.d/php.ini
```

- Configure WebApp to use this Startup Script/Command from __Configurations__ blade of the WebApp.

- Restart the WebApp. Validate if the ionCube loader has been enabled either using __phpinfo()__ method or php -v command:

```php
root@ea5dece91a58:/home# php -v
Cannot load Zend OPcache - it was already loaded
PHP 7.2.24 (cli) (built: Nov  5 2019 13:45:32) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
    with the __ionCube PHP Loader__ + ionCube24 v10.3.9, Copyright (c) 2002-2019, by ionCube Ltd.
    with Zend OPcache v7.2.24, Copyright (c) 1999-2018, by Zend Technologies
```