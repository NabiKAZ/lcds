![Project logo](https://raw.githubusercontent.com/jf-guillou/lcds/master/web/images/lcds_logo-200.png)

# Light Centralized Digital Signage

This PHP web app is a simple digital signage manager, used to display content on any kind of browser.

Based on the [Yii2 framework](http://www.yiiframework.com/).
See [https://github.com/jf-guillou/lcds/blob/master/composer.json](composer.json) for the complete list of extensions used in this project.

[![Build Status](https://travis-ci.org/jf-guillou/lcds.svg?branch=master)](https://travis-ci.org/jf-guillou/lcds) [![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/jf-guillou/lcds/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/jf-guillou/lcds/?branch=master) [![GitHub license](https://img.shields.io/badge/license-New%20BSD-blue.svg)](https://raw.githubusercontent.com/jf-guillou/lcds/master/LICENSE.md)

**Table of contents**
- [Requirements](#requirements)
- [Installation](#installation)
  - [Install app](#install-app)
  - [Database](#database)
  - [App configuration](#app-configuration)
  - [Web Server configuration](#web-server-configuration)
    - [Apache](#apache)
    - [Nginx](#nginx)
  - [Ready](#ready)
- [Upgrade](#upgrade)
- [Client](#client)
- [Raspberry Pi Client](#raspberry-pi-client)
  - [Client installation](#client-installation)
  - [Auto-Configuration](#auto-configuration)
  - [Manual configuration](#manual-configuration)

## REQUIREMENTS

- PHP >= 7.0
    - php-curl
    - php-xml
    - php-mbstring
    - php-ldap
- Any php-pdo supported driver and associated database
    - php-mysql with mariadb-server >= 10.0 preferably
- mediainfo -- extract media content-type and duration
- youtube-dl -- download media from video hosting sites
- [Composer](https://getcomposer.org/)

## INSTALLATION

### Requirements

```bash
apt-get install -y git php php-mbstring php-ldap php-mysql php-curl php-xml composer mariadb-server mediainfo

sudo wget https://yt-dl.org/downloads/latest/youtube-dl -O /usr/local/bin/youtube-dl
sudo chmod a+rx /usr/local/bin/youtube-dl
```

### Install app

```bash
cd /var/www
git clone -b production https://github.com/jf-guillou/lcds.git
cd lcds
composer install --no-dev
```

### Database

Edit the database configuration file `config/db.php` according to your settings.
> Make sure to modify the `dbname=`, `username` and `password`.

Then run the migrations to pre-fill the database :
```bash
./yii migrate --interactive=0
```

### App configuration

Edit the app configuration file `config/params.php` :

- `language` - Currently supported :
  - `en-US`
  - `fr-FR`
- `prettyUrl` - See [Enable pretty URLS](#enable-pretty-urls)
- `cookieValidationKey` - Should be automatically generated by composer, should not be modified
- `proxy` - Outgoing proxy if necessary, used by media downloaders
- `useSSO` - If the web server is SSO enabled (CAS/Kerberos)
- `ssoEnvUsername` - The HTTP environment variable containing the username from SSO
- `useLdap` - Use LDAP for authentication backend
- `ldapOptions` - Common options for LDAP, default values should help you understand their meaning
- `cookieDuration` - Cookie duration in seconds, 0 will disable cookies
- `weather` - Weather renderer configuration / See https://darksky.net/dev for more details
  - `language` - Summary text language
  - `units` - Temperature units
  - `apikey` - API key used to fetch weather status
  - `withSummary` - Display text summary alongside icon

### Web Server configuration

The [Yii2 framework](http://www.yiiframework.com/) document root is in the /web folder

#### Apache
```
DocumentRoot "/var/www/lcds/web"
```

##### Enable pretty urls
Add to Apache configuration
```
<Directory "/var/www/lcds/web">
    # use mod_rewrite for pretty URL support
    RewriteEngine on
    # If a directory or a file exists, use the request directly
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    # Otherwise forward the request to index.php
    RewriteRule . index.php
    # ...other settings...
</Directory>
```

#### Nginx
```
root /var/www/lcds/web;
```

##### Enable pretty urls
Add to Nginx configuration
```
location / {
    # Redirect everything that isn't a real file to index.php
    try_files $uri $uri/ /index.php$is_args$args;
}
```

### Ready

The app should be ready to use.

Default credentials are `admin`/`admin`

Do not hesitate to report bugs by posting an [issue at Github](https://github.com/jf-guillou/lcds/issues)


## UPGRADE

```bash
cd /var/www/lcds
git pull
composer install --no-dev
./yii migrate --interactive=0
```

## CLIENT

The client configuration mostly depends on the hardware you are going to use.

Basically, just set the homepage of the browser to your web server address with '/frontend' suffix

`https://lcds-server/frontend`

### Raspberry Pi Client

If you want to use a Raspberry Pi, here is [the setup we use](https://github.com/jf-guillou/lcds-rpi-client).
