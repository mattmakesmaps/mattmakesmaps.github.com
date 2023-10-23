---
title: "Using pg_dump with PostGIS Topology"
date: 2014-01-15
---

Using the [pg_dump](http://www.postgresql.org/docs/0.0/static/app-pgdump.html) utility is a standard
method for backing up a PostgreSQL database. Furthermore, limiting a `pg_dump` export to a particular schema increases the portability of your geospatial data across different versions of PostGIS.
Since the PostGIS function signatures themselves are not copied in the data backup, data can be
backed up from a database under one version of PostGIS and restored into a new database of a differing version. [Boundless](http://workshops.boundlessgeo.com/postgis-intro/backup.html) has a great intro
guide to backing up and restoring PostGIS databases.

Working with PostGIS Topologies however, requires a special tweak. <!-- more --> A typical PostGIS topology contains
data spread across three schemas:

- Topology 'Metadata' schema, containing registered topologies and their layers.
Created by executing the statement `CREATE EXTENSION postgis_topology;`.
- Schema containing topology primitives. This is created as the result of a call to
[CreateTopology()](http://postgis.net/docs/CreateTopology.html)
- Schema containing topogeometry columns. These would be created by a call to
[AddTopoGeometryColumn](http://postgis.net/docs/AddTopoGeometryColumn.html)

As an example, we'll have a PostGIS database called `parcels_postgis` with the following:

- Topology extension enabled
- Topology primitives are stored in the `parcel_topology` schema.
- Topogeometry columns are stored in the `parcels` schema

Under this example, a call to `pg_dump` looks like the following:

```bash
pg_dump --schema=topology --schema=parcels --schema=parcel_topology --file=parcel_data.sql parcels_postgis 
```

Restoring to a new db however, produces the following error:

```bash
psql:test_data.sql:644: ERROR:  insert or update on table "layer" violates foreign key constraint "layer_topology_id_fkey"
DETAIL:  Key (topology_id)=(1) is not present in table "topology".
```

This is because the `pg_dump` utility defaults to INSERT'ing records into the `topology` schema's
`layer` table **before** INSERT'ing records into the `topology` schema's `topology` table. Since
the `layer` table has a foreign key constraint with the `topology` table, this can never work properly.

One solution therefore, is to manually edit your `pg_dump` output file to run `COPY` commands onto
the `topology.topology` table first, then the `topology.layer` table second. A modified script with
the highlighted changes can be seen [here](https://github.com/mattmakesmaps/travis-postgis-test/blob/master/test_data.sql#L633-L651).