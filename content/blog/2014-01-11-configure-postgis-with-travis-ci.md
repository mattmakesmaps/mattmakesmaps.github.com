---
title: "Configure PostGIS with Travis CI"
date: 2014-01-11
---
Thanks to a great presentation by [foundatron](https://github.com/foundatron) on the continuous
integration framework, [Travis CI](https://travis-ci.org), I've finally been motivated to push
for higher test coverage in my python projects.

Some of these [projects](https://github.com/mattmakesmaps/pgsql2gist) require connectivity to a
PostGIS database for testing, and with Travis CI that process is extremely straightforward.
This post includes notes on configuring a Travis CI environment with PostgreSQL and PostGIS, then
loading that empty database with data.

### PostgreSQL/Postgis Versions
**NOTE:** These versions are current as of **January 2014**.

* PostgreSQL: 9.1.11 by default, although you can [explicitly declare](http://about.travis-ci.org/blog/2013-11-29-PostgreSQLql-92-93-now-available/) 9.2 or 9.3.
* PostGIS: 2.1.1 r12113
* GEOS: 3.3.8
* PROJ: 4.8.0
* GDAL: 1.9.2
<!-- more -->
### Configuration

**NOTE:** For background reading on Travis CI, check out the [Getting Started](http://about.travis-ci.org/docs/user/getting-started/) and [Build Configuration](http://about.travis-ci.org/docs/user/build-configuration/) docs.

As stated in the [database docs](http://about.travis-ci.org/docs/user/database-setup/#PostgreSQLQL),
a Travis CI environment comes with PostgreSQL installed and ready-to-go. This also includes supporting
utilities such as: `psql`,`pg_dump`, and `pg_restore`. Since we're using PostgreSQL 9.1+ and PostGIS 2.0+,
PostGIS can be installed using the `CREATE EXTENSION` syntax.

The `.travis.yml` configuration file should now be modified to execute `psql` calls in the
`before_script` block to:

1. Create the database
2. Create the PostGIS/PostGIS Topology Extensions
3. Populate the database with test data. (See Below for Instructions)

A reference example `.travis.yml` looks like this:

```yml
language: python
python:
  - "2.7"

# command to install dependencies
install:
  - "pip install -r requirements.txt --use-mirrors"

# database creation
before_script:
  - psql -c 'create database travis_postgis;' -U postgres
  - psql -c 'CREATE EXTENSION postgis;' -U postgres -d travis_postgis
  - psql -c 'CREATE EXTENSION postgis_topology;' -U postgres -d travis_postgis
  - psql -f test_data.sql -U postgres -d travis_postgis

# command to run tests
script: nosetests --verbose
```

### Creating Test Data

The `pg_dump` utility can be used to export the contents of a PostGIS database into a sql file,
suitable for loading into a newly created database by the Travis worker. Key to this is to have testing
data that you want to export inside of a separate schema (e.g. not in `public`). With `pg_dump` we can
specify an individual schema of data tables to export, otherwise we'd be stuck with exporting potentially
incompatible PostGIS function signitures in addition to our data. Boundless has a 
[good article](http://workshops.boundlessgeo.com/postgis-intro/backup.html) on backup strategies for PostGIS.

From our source PostGIS database, export tables from our schema of interest (`test_data`) into `test_data.sql`

``` bash
$ pg_dump --schema=test_data travis_postgis > test_data.sql
```

As seen in the section above, the resulting sql file, `test_data.sql` is then executed as part of the
Travis `before_script` block, populating the testing database with geometries.

**NOTE:** I've used the same name `travis_postgis` for both my local database as well as the testing
database used by Travis CI. This isn't required. 

### Wrap Up

Thanks to the combination of PostgreSQL's easy to use `CREATE EXTENSION` syntax along with Travis CI's flexible build configurations, setting up a custom PostGIS database is fairly simple.

An example repository with a `.travis.yml` file and an example unit test can be found here: https://github.com/mattmakesmaps/travis-postgis-test

This repository's Travis CI build log can be found here: https://travis-ci.org/mattmakesmaps/travis-postgis-test
