---
title: "Quickstart GIS Setup on Ubuntu Linux 12.04 LTS"
date: 2013-08-26
---

I had to recently setup a clean Ubuntu 12.04 LTS workstation for GIS
data processing. For the simplicity and speed, I decided to go with
packaged versions of many of the required GIS software. Many of the
packages are derived from the [ubuntugis-unstable](https://launchpad.net/~ubuntugis/+archive/ubuntugis-unstable) repository.

What follows below are a blow-by-blow of my installation notes
grouped by software package. Either code snippets or links to relevant
blog posts.<!-- More -->

This is certainly only a high-level guide. If anyone has any other
suggestions or alternatives, please feel free to list them.

**NOTE:** The order of these installs reflects the actual order in
which I installed them.

## Non-GIS Essentials

### Install SSH

[SSH](http://en.wikipedia.org/wiki/Secure_Shell) or Secure Shell,
is essential for remote server administration.

`$ sudo apt-get install openssh-server`

### Increase History Size

The default history size is pretty low. Increasing the size allows us
to do search for that oft-used terminal command right when you need it
most. What was the name of that server I ssh'd into last month?
Piping history to grep can do the trick. `$ history | grep -i "ssh"`

```bash
$ vim ~/.bashrc
HISTSIZE=10000
HISTFILESIZE=20000
```

### Python Virtual Environments

Used this [awesome gist](https://gist.github.com/panuta/3075882) by
github user [panuta](https://github.com/panuta).

Didn't follow, but a good reference: [http://askubuntu.com/questions/244641/how-to-set-up-and-use-a-virtual-python-environment-in-ubuntu](http://askubuntu.com/questions/244641/how-to-set-up-and-use-a-virtual-python-environment-in-ubuntu)

### Apache

```
$ sudo apt-get install apache2
```

## GIS Packages

### Connect to the UbuntuGIS-Unstable PPA

In order to access many of these packages, we need to connect the
system to the UbuntuGIS-Unstable PPA.

```
$ sudo add-apt-repository ppa:ubuntugis/ubuntugis-unstable
$ sudo apt-get update
```
### GDAL/Fiona

GDAL/OGR are the defacto Open Source GIS swiss army knife.
[Fiona](https://github.com/sgillies/Fiona) provides easy-to-use
Python bindings for use in your projects.

```
$ sudo apt-get install libgdal-dev gdal-bin
$ workon gis-base
$ sudo apt-get install python2.7-dev
$ pip install Fiona
```

### Postgres/PostGIS

The ubuntu unstable repository provides pre-packaged binaries combining both Postgres and Postgis.

As of August 2013, GDAL version 1.10 for some reason doesn't include libgdal1, replaced by libgdalh. See: [http://lists.osgeo.org/pipermail/ubuntu/2013-July/000757.html](http://lists.osgeo.org/pipermail/ubuntu/2013-July/000757.html)
This appearently is causing some issues with packages installations, including Postgres/PostGIS. The steps below appear to work.

```
$ sudo apt-get install libgdal1-dev
$ sudo apt-get install postgresql-9.1-postgis-2.0
```

If you need any of the postgis util scripts, e.g. `postgres_restore.pl` used for restoring a postgres database (across differing versions
of postgis), I believe the only way to get these is by making from source.

```
$ sudo apt-get install proj
$ wget http://download.osgeo.org/postgis/source/postgis-2.0.3.tar.g
$ tar -zxvf postgis-2.0.3.tar.gz
$ cd postgis-2.0.3/
$ ./configure
$ make
```

Finally, if you need to change your Postgresql data dir to another
disk, this guide provides good advice: [http://www.whiteboardcoder.com/2012/04/change-postgres-datadirectory-folder.html](http://www.whiteboardcoder.com/2012/04/change-postgres-datadirectory-folder.html)

### Imposm 2.5.0

If you're rendering OSM data, imposm is one of the fastest ways
to get data from an OSM Planet Extract into PostGIS.

I followed the instructions within the [Imposm documentation](http://imposm.org/docs/imposm/latest/install.html#requirements)

Note: Installed imposm directly into my virtualenv "gis-base"

### Quantum GIS

```bash
$ sudo apt-get install qgis
```