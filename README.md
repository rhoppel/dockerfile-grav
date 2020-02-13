# Official Docker Image for Grav

This currently is pretty minimal and uses:

* apache-2.4.38
* GD library
* Unzip library
* php7.3
* php7.3-opcache
* php7.3-acpu
* php7.3-yaml
* cron
* vim editor

## Building the image from Dockerfile

```
docker build -t grav:latest .
```

## Running Grav Image with Latest Grav + Admin:

```
docker run -p 8000:80 grav:latest
```

Point browser to `http://localhost:8000` and create user account...

## Running Grav Image with Latest Grav + Admin with a named volume (can be used in production)

```
docker run -d -p 8000:80 --name grav --restart always -v grav_data:/var/www/html grav:1.0
```

## My "raspi2b" instance

```
sudo docker run -d -p 81:80 --name grav-1 --restart always -v grav_data:/var/www/html grav:latest
```

```
pi@raspi2A (master)✗ > sudo ls -l  /var/lib/docker/volumes/grav_data/_data
            
total 420
drwxrwxr-x  2 www-data www-data   4096 Feb 12 18:31 assets
drwxrwxr-x  2 www-data www-data   4096 Feb 12 18:31 backup
drwxrwxr-x  2 www-data www-data   4096 Feb 12 18:31 bin
drwxrwxr-x  6 www-data www-data   4096 Feb 12 18:35 cache
-rw-rw-r--  1 www-data www-data 140369 Feb 11 18:20 CHANGELOG.md
-rw-rw-r--  1 www-data www-data   3216 Feb 11 18:20 CODE_OF_CONDUCT.md
-rw-rw-r--  1 www-data www-data   3850 Feb 11 18:20 composer.json
-rw-rw-r--  1 www-data www-data 200405 Feb 11 18:20 composer.lock
-rw-rw-r--  1 www-data www-data   7098 Feb 11 18:20 CONTRIBUTING.md
drwxrwxr-x  2 www-data www-data   4096 Feb 12 18:31 images
-rw-rw-r--  1 www-data www-data   1710 Feb 11 18:20 index.php
-rw-rw-r--  1 www-data www-data   1071 Feb 11 18:20 LICENSE.txt
drwxrwxr-x  2 www-data www-data   4096 Feb 12 18:31 logs
-rw-rw-r--  1 www-data www-data     76 Feb 11 18:20 now.json
-rw-rw-r--  1 www-data www-data   6701 Feb 11 18:20 README.md
-rw-rw-r--  1 www-data www-data    227 Feb 11 18:20 robots.txt
drwxrwxr-x 10 www-data www-data   4096 Feb 12 18:31 system
drwxrwxr-x  2 www-data www-data   4096 Feb 12 18:31 tmp
drwxrwxr-x  8 www-data www-data   4096 Feb 12 18:31 user
drwxrwxr-x 29 www-data www-data   4096 Feb 12 18:31 vendor
drwxrwxr-x  2 www-data www-data   4096 Feb 12 18:31 webserver-configs
```
### raspberry pi docker development

#### persistent data
- **docker:**  /var/lib/docker/volumes/grav_data
- **pi:**      /home/pi/websites/html/_data

#### /etc/fstab [data sharing between docker and "pi" user]
```
*pi@raspi2A (master)✗* > cat /etc/fstab 
proc                  /proc           proc    defaults          0       0
PARTUUID=cda682ca-01  /boot           vfat    defaults          0       2
PARTUUID=cda682ca-02  /               ext4    defaults,noatime  0       1
# a swapfile is not a swap partition, no line here
#   use  dphys-swapfile swap[on|off]  for that
# bindfs is the magic
bindfs#/var/lib/docker/volumes/grav_data /home/pi/websites/html fuse force-user=pi,force-group=pi,create-for-user=www-data,create-for-group=www-data,create-with-perms=0770,chgrp-ignore,chown-ignore,chmod-ignore 0 0

```
#### clean persistent data from raspberry pi
```
rm -rf /home/pi/websites/html/_data/*
```

## bindfs: Development sharing data

### Installing bindfs (just the first time)
```
pi@raspi2b $ apt-get update
pi@raspi2b  $ apt-get -y install bindfs
```
### Creating the application mountpoint
```
pi@raspi2b  $ mkdir -p /home/pi/websites/html
pi@raspi2b  $ chown -Rf pi:pi /home/pi/websites
pi@raspi2b  $ chmod -Rf 770 /home/pi/websites
```
Then, edit the content of /etc/fstab and add this line (just one line, without line wraps):
```
bindfs#/var/www/html /home/pi/websites/html fuse force-user=pi,force-group=pi,create-for-user=www-data,create-for-group=www-data,create-with-perms=0770,chgrp-ignore,chown-ignore,chmod-ignore 0 0
```
Save the file, and proceed with mounting application (will mount automatically at system load):
```
pi@raspi2b $ mount /home/pi/websites/html
```
