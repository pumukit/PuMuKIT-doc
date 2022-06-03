# PuMuKIT Installation Guide

## Index

1. [Requirements](#1-requirements)
2. [Single-node Installation](#2-single-node-installation)
3. [Multi-node Installation](#3-multi-node-installation)
4. [Opencast integration](#4-opencast-integration)
5. [F.A.Q.](#5-faq)

## 1. Requirements

PuMuKIT is a LEMP (Linux, nginx, MongoDB, PHP) application, created with the Symfony framework. It uses libav-tools (or ffmpeg) to analyze the audiovisual data, as well as to transcode the data.

Newest versions of PuMuKIT ( >= 3.6.0 ) use docker to make easy installations on any S.O with virtualization.

Requirements are:

1. [Docker compose](https://docs.docker.com/compose/install/)
2. [Git](https://github.com/git-guides/install-git)

## 2. Single-node Installation

In this section we will describe how to install PuMuKIT in a single server (node).
A single-node deployment of PuMuKIT is usually recommended for development, demonstration and small production environments.

Follow the below instructions. If any error is thrown check the [F.A.Q. section](#5-faq) or ask on PuMuKIT community list:

1. Install requirements step.

2. Clone PuMuKIT software
```
git clone git@github.com:pumukit/PuMuKIT.git
```

4. Build containers

Go to PuMukIT project folder and execute: 
```
docker-compose build
```

3. Start containers
```
docker-compose up -d 
```

If all are fine, you will have the same containers as [docker-compose.yml](https://github.com/pumukit/PuMuKIT/blob/master/docker-compose.yml) file describes.

4. Connect and enjoy

    * Connect to the WebTV portal here: `https://{PuMuKIT-HOST}/`
    * Connect to the Admin UI with the default user created: `https://{PuMuKIT-HOST}/admin`

User created by default on PuMuKIT is defined on .env file and using the following variables:
```
PUMUKIT_USER
PUMUKIT_PASS
PUMUKIT_USER_MAIL
```

[(back to index)](#index)

## 3. Multi-node Installation

In this section we will describe how to install PuMuKIT in a multi server (multi-node) environment. A multi-node deployment of PuMuKIT is usually recommended for real production environments.

On a default single-node installation, the transcoder, the storage of the multimedia files and the database are
installed in the same node as PuMuKIT. A different installation can be done. It is possible to set up external nodes
to install a transcoder or manage the storage of the multimedia files in a NAS (Network Attached Storage) server.

To perform a multi-node installation of PuMuKIT, first you need to install PuMuKIT in a node according to
[Single-node Installation on Linux Ubuntu 18.04](#2-single-node-installation).

After having a single-node deployment you can improve it with additional nodes to take care of storage and/or transcoding jobs:


#### 3.1. External PuMuKIT-Transcoders

On a default standalone installation, there is only one PuMuKIT transcoder installed in the same node as PuMuKIT core.

One or multiple transcoders can be installed in different nodes. An external transcoder node can be configured with the
following recommendations:

- Ubuntu 18.04 although another operating system that accomplish the following recommendations can be used.
- 8 CPUs
- 8 GB RAM
- 20 GB HDD
- libav / ffmpeg libraries: the ones you have configured into the `app/config/encoder.yml` file of your PuMuKIT node.
- Being in the same network as the PuMuKIT and NAS nodes.

Configure the transcoder node as a web server with nginx or Apache.

Copy the [webserver.php file](https://github.com/pumukit/PuMuKIT/blob/3.6.x/doc/conf_files/cpus/webserver.php) of the PuMuKIT node into the web folder of your transcoder node server directory.
For instance, in a default nginx server configuration on an Ubuntu 18.04 server, should be in `/usr/share/nginx/html/webserver.php`. Change the default user and password of this webserver configuration
by opening the file and changing these two lines:

```
define("USER", "pumukit");
define("PASSWORD", "PUMUKIT");
```

Give access to the storage system (On PuMuKIT server or External NAS).

Configure the `app/config/encoder.yml` file of your PuMuKIT node to add the transcoder server in the `cpus` section.
To see all the options to configure, type on your PuMuKIT node and see the `cpus` section:

```
cd /path/to/pumukit
php bin/console config:dump-reference pumukit_encoder
```

```
    cpus:

        # Prototype
        name:

            # Encoder Hostnames (or IPs)
            host:                 ~ # Required

            # Deprecated since version 2, to be removed in 2.2.
            number:               1

            # Top for the maximum number of concurrent encoding jobs
            max:                  1

            # Type of the encoder host (linux, windows or gstreamer)
            type:                 ~ # One of "linux"; "windows"; "gstreamer"

            # Specifies the user to log in as on the remote encoder host
            user:                 ~

            # Specifies the user to log in as on the remote encoder host
            password:             ~

            # Encoder host description
            description:          ''

            # Valid profiles
            profiles:          
```

* `name`: name of your transcoder server. E.g.: transcoder. Replace the word `name` with the name you give.
* `host`: IP or hostname of your transcoder server
* `max`: maximum number of concurrent jobs
* `type`: type of the transcoder host
* `user`: username defined in the webserver.php file of your transcoder server
* `password`: password defined in the webserver.php file of your transcoder server
* `profiles` (optional): array of valid profiles. If this parameter exists, the cpu will only accept tasks for the specified profiles.

You can add as many transcoders as you want to the configuration of the encoders of PuMuKIT.
Define each transcoder with a different `name` and add host, max, type, user and password configuration.


#### 3.2. External Storage or NAS (Network Attached Storage) Server

Configure your NAS server to give access to PuMuKIT and transcoder nodes according to:

- PuMuKIT uploads, inbox and tmp directories. Defaults:
    - pumukit.uploads_dir: '%kernel.root_dir%/../web/uploads'
    - pumukit.inbox: '%kernel.root_dir%/../web/storage/inbox'
    - pumukit.tmp: '%kernel.root_dir%/../web/storage/tmp'

- Encoders configuration: `dir_out` parameter of each profile defined in your `app/config/encoder.yml` configuration file.

PuMuKIT node and transcoder node should mount the NAS server folder where all the multimedia files are shared.


[(back to index)](#index)

## 4. Opencast integration

To add [Opencast](https://www.opencast.org/) integration features to the PuMuKIT platform use the Opencast Bundle. The Opencast Bundle is included in PuMuKIT standard installation but it's not activated by default. To activate and configure it, follow the instructions in the [Opencast bundle](https://github.com/pumukit/PumukitOpencastBundle/blob/master/README.md)


## 5. F.A.Q.

**Configure max upload filesize**

1.- If you get a 413 response status code (request entity too large) and get "client intended to send too large body" in NGINX log. Check the NGINX conf file (/etc/nginx/sites-available/default)

```
client_max_body_size 2000m;
client_body_buffer_size 128k;
```

2.- If you get a message like "The file XXXX.avi exceeds your upload_max_filesize ini directive". Check the php-fpm conf file (/etc/php/7.2/fpm/php.ini)

```
upload_max_filesize = 2000M
post_max_size = 2000M
```

3.- If you get a 504 "Gateway Time-out", change the php/7.2-fpm configuration. Chose the same value for `request_terminate_timeout` in `/etc/php/7.2/fpm/pool.d/www.conf` and `max_execution_time` in `/etc/php/7.2/fmp/php.ini`files. For example:

* /etc/php/7.2/fpm/pool.d/www.conf

```
request_terminate_timeout = 30
```

* /etc/php/7.2/fpm/php.ini

```
max_execution_time = 30
```

* Restart php/7.2-fpm and NGINX:

```
sudo service php7.2-fpm restart
sudo service ningx restart
```


**Setting up Permissions?**

 * https://symfony.com/doc/3.4/setup.html#checking-symfony-application-configuration-and-setup
 * https://symfony.es/documentacion/como-solucionar-el-problema-de-los-permisos-de-symfony2/
 * Setting up ownership of upload directories

    ```
    sudo chown -R www-data:www-data web/storage/ web/uploads/
    ```


**Enable MongoDB text index**

Use MongoDB 2.6 or upper, the text search feature is enabled by default. In Ubuntu 18.04 you can install the last version of MongoDB using the next documentation:

 * https://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/


#####Add GitHub token

In case the `php composer.phar install` asks for a token with a  message like:

```
Could not fetch https://api.github.com/repos/VENDOR_NAME/NAME_Bundle/zipball/HASH_CODE, please create a GitHub OAuth token to go over the API rate limit
Head to https://github.com/settings/tokens/new?scopes=repo&description=Composer+on+HOST_ID+DATE+TIME
to retrieve a token. It will be stored in "/home/USER/.composer/auth.json" for future use by Composer.
Token (hidden):
```

There is a rate limit on GitHub's API for downloading repos. In case of reaching that limit, Composer prompts the above message asking for authentication to download a repo.

It is necessary to create a token and add it to composer.

1. Create an OAuth token on GitHub. Follow the instructions on [GitHub Help page](https://help.github.com/articles/creating-an-access-token-for-command-line-use/).

2. Add the token to the configuration running:

```
cd /var/www/pumukit
php composer.phar config -g github-oauth.github.com <oauthtoken>
```

Now Composer should install/update without asking for authentication.


**Not allowed to access app_dev.php via web**

If you get this message when trying to access https://{PuMuKIT-HOST}/app_dev.php:
```
You are not allowed to access this file. Check app_dev.php for more information.
```

Comment the following code in `web/app_dev.php` file:

```php
/*
// This check prevents access to debug front controllers that are deployed by accident to production servers.
// Feel free to remove this, extend it, or make something more sophisticated.
if (isset($_SERVER['HTTP_CLIENT_IP']) || isset($_SERVER['HTTP_X_FORWARDED_FOR']) || !(in_array(@$_SERVER['REMOTE_ADDR'], array('127.0.0.1', 'fe80::1', '::1')) || php_sapi_name() === 'cli-server')
) {
header('HTTP/1.0 403 Forbidden');
exit('You are not allowed to access this file. Check '.basename(FILE).' for more information.');
}
*/
```


**Not allowed to access config.php via web**

If you get this message when trying to access https://{PuMuKIT-HOST}/config.php:
```
This script is only accessible from localhost.
```

Comment the following code in `web/config.php` file:

```php
/*
if (!in_array(@$_SERVER['REMOTE_ADDR'], array(
    '127.0.0.1',
    '::1',
))) {
    header('HTTP/1.0 403 Forbidden');
    exit('This script is only accessible from localhost.');
}
*/
```


**Internal Server Error (500) when searching a series or a multimedia object**

The mongodb text index is needed for searching. And this error is produced because the text index is not created:

```
Cannot run command count(): error processing query: ns=pumukit___.MultimediaObject limit=0 skip=0
...
planner returned error: need exactly one text index for $text query
```

To generate the index execute the following command:

```
php bin/console doctrine:mongodb:schema:create --index
```

[(back to index)](#index)
