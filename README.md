# Docker container for the MusicBrainz database

[![Docker Hub](https://img.shields.io/badge/docker-ready-blue.svg)](https://registry.hub.docker.com/u/arey/musicbrainz-database/) 

Based on the official Postgres image, this docker image installs a PostgreSQL 9.5.10 wilh all librairies required by the MusicBrainz database.

This image comes with a create-database.sh shell script used to create and feed the database:

* Create the musicbrainz schema
* Create all tables
* Import the last MusicBrainz database export
* Add index and primary keys


SQL scripts are retrieved from the [MusicBrainz Github repo](https://github.com/metabrainz/musicbrainz-server/tree/master/admin/sql).

## Build the image

The arey/musicbrainz-database image is published to Docker Hub. Docker will automatically download it. If you prefer to build it manually after retrieving source code from GitHub:
```
git clone https://github.com/arey/musicbrainz-database.git
cd musicbrainz-database
docker build -t arey/musicbrainz-database .
```

## Run the container and import the musicbrainz data

```
docker run -t -d -p 5432:5432 --name musicbrainz-database -e POSTGRES_USER=musicbrainz -e POSTGRES_PASSWORD=musicbrainz arey/musicbrainz-database
```

If you have host-based storage available for the Postgres DB files (recommended), adjust the above base command with a this parameter:
-v /path/to/storage/location:/var/lib/postgresql

```
docker run -it --link musicbrainz-database:postgresql -e POSTGRES_USER=musicbrainz -e POSTGRES_PASSWORD=musicbrainz --rm arey/musicbrainz-database /create-database.sh
```

## Test it

In order to test your database installation, execute the below SQL query:
```
docker run -it --link musicbrainz-database:postgresql --rm arey/musicbrainz-database sh -c 'exec psql -h postgresql -d musicbrainz -U musicbrainz -a -c "SELECT COUNT(*) FROM artist"'
```
Enter the 'musicbrainz' password. A minimum number of 995899 artists should be counted.


```
# or get bash for container
docker exec -it musicbrainz-database /bin/bash
# then test queries
psql -h localhost -d musicbrainz -U musicbrainz -a -c "SELECT * FROM artist where name = 'Built to Spill'"
```

##  Connect to the database

From Java application, use the following JDBC URL: jdbc:postgresql://localhost:5432/musicbrainz and the credentials: musicbrainz / musicbrainz

Note: For Microsoft Windows or MacOS users, please replace localhost with the boot2docker IP.

