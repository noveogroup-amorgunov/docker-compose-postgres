# docker-compose-postgres

Easy way work with *postgres* using *docker-compose*.

- Firstly, install [docker](https://docs.docker.com/engine/installation/) and [docker-compose](https://docs.docker.com/compose/install/).
- Edit `postgres-initdb.sh` file (change user/password and database name as you want):

```bash
#!/bin/sh -e

psql --variable=ON_ERROR_STOP=1 --username "postgres" <<-EOSQL
    CREATE ROLE events WITH LOGIN PASSWORD 'events';
    CREATE DATABASE "events-api" OWNER = events;
    GRANT ALL PRIVILEGES ON DATABASE "events-api" TO events;
EOSQL
```

- Edit exported port in `docker-compose.yml` (if you want):

```yml
postgres:
  ports:
    - '5436:5432' # 5436 <- change this value
  restart: always
  image: postgres:9.6.5-alpine
  volumes:
    - ./postgres-initdb.sh:/docker-entrypoint-initdb.d/init.sh
```

Run command `docker-compose up` or `docker-compose up -d` to run container as process. To stop container run `docker-compose down`.

Now you can work with database using follow connection url: `postgres://events:events@localhost:5436/events-api`.


### Solutions for possible problems

- Postgresql isn't exucate init database file: try to reinstall volume (reference: https://github.com/docker-library/postgres/issues/203#issuecomment-255200501)

```bash
docker volume ls
docker volume rm <VOLUME>
```

- If you have local postgresql, may be you should turn off postgresql service as:

```bash
sudo service postgresql stop
```
