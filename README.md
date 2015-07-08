# NOC Project Docker container

Docker container for the [NOC Project](https://kb.nocproject.org/).

## Quickstart
Note that if the containers are not already downloaded, this will take a few minutes to complete.

```sh
curl -O https://raw.githubusercontent.com/abn/noc-docker/master/noc
chmod 755 noc

# this starts the database containers and the data container
./noc start

# upgrade before running in order to finalize onfiguration
./noc upgrade
./noc runserver
```

Once this is complete, you should be able to access NOC by visiting http://localhost:8000.

## Configuring NOC
The `noc` helper script sets up your environment such that an instance named `noc-data` is used to persist data between runs. To edit configuration, you can load this up in any container with your favorite editor and modify files located at `/opt/noc/etc`.

```sh
docker run --rm -it --volumes-from noc-data fedora:22 bash
```

This command will drop you into a shell with the data mounted.

## Configuring Database Passwords
The database passwords can be configured by either modifying the `noc` script or by using the environment variables `PG_PASSWORD` and/or `MONGO_PASSWORD` before initializing.

## Accessing databases

### Postgres
```sh
docker run -it --link noc-postgres:postgres \
    -e "PGPASSWORD=${PG_PASSWORD}" \
    -e "COMMAND=${COMMAND}" \
    --rm postgres bash -c 'psql -h "$POSTGRES_PORT_5432_TCP_ADDR" -p "$POSTGRES_PORT_5432_TCP_PORT" -U noc'
```
### MongoDB
```sh
docker run -it --link noc-mongo:mongo \
    -e "COMMAND=${MONGO_CREATE_USER}" \
    --rm mongo bash -c 'mongo "$MONGO_PORT_27017_TCP_ADDR:$MONGO_PORT_27017_TCP_PORT/noc"'
```
