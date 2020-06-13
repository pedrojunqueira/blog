---
title: "Understanding Docker volumes with a practical example"
date: 2020-04-17T06:55:35+09:30
draft: false
toc: false
images:
tags:
  - docker
  - postgresql
  - sql
---

Docker is here to stay and I see it more and more part of the work of any developer and I am impressed to how Docker can make your life so easy.

This post is not a What is Docker one or an introduction. 

You can read it [here](https://docs.docker.com/get-started/overview/) or just watch this very good video from [NetworkChuck](https://www.youtube.com/watch?v=eGz9DS-aIeY&t=494s) then you will have a good grasp of its core concepts and what this is for.

My interest in Docker comes from my data analysis and data scientist background.

When I started to develop web services applications for machine learning in production Docker came very handy.

One issue that I came across when learning docker was that a Docker container life cycle is ephemeral because all its components like data, writable layer, network, ports set up and etc are gone once the container is gone, including all the data.

What!?

Yes.

Just to illustrate an example. Say you developed an app in Flask that has a back end database in psql and you decide to containerize this and ship. It all works fine and data gets stores inside the container. However, eventually you wanted to do some changes in the app like add new features of fix some bugs etc. Then it is necessary to rebuild the image. You know what happens when this is done?

All the data that was inside the old container is gone

How do we solve this?

Well this is why it is important to understand volumes because this is the way you can persist data of your project that lives outside the life cycle of your container.

There are 2 ways to solve this problem, actually 3 but will talk about 2 here.

1. Volume
2. Bind Mount

What both of the solutions do is to make sure that any part of your container data, and you define what, is actually outside the container because there is a "link" that allow your container access this data.

![Docker](https://docs.docker.com/storage/images/types-of-mounts-volume.png)
[source](https://docs.docker.com/storage/volumes/)

The difference from volume and bind mount is that:

* Volume lives in the host machine, inside Docker file system, and it is the recommended option by Docker and there are some advantages and it is listed below why.
* Bind Mount also lives in the host machine however the developer put is wherever he/she wants and there are some limitations

According to Docker [documentation](https://docs.docker.com/storage/volumes/) here are the advantages of Volume over Bind Mount

* Volumes are easier to back up or migrate than bind mounts.
* You can manage volumes using Docker CLI commands or the Docker API.
* Volumes work on both Linux and Windows containers.
* Volumes can be more safely shared among multiple containers.
* Volume drivers let you store volumes on remote hosts or cloud providers, to encrypt the contents of volumes, or to add other functionality.
* New volumes can have their content pre-populated by a container

I will give an example now, using volumes, so this may help you to solidify your understanding.

I will create 3 containers with a Postgresql image running in different ports in the development machine, however all those 3 containers you share the same volume i.e., the same data. The example is just for illustration of how volumes work.

You do **not** need to have a Postgresql instance installed on your host/development system and all that you need is Docker installed.

Just check if you have Docker already:

```
$docker -v
Docker version 19.03.8, build afacb8b
```
If not it is easy to install [Docker](https://docs.docker.com/get-docker/).

For this lets create a folder to test those concepts

`mkdir docker_volume`
then cd into it
`cd docker_volume`

create a shell script file so it will save some typing

`touch commands.sh`

Then write on the .sh file

```bash
#!/bin/bash

docker run --name psql1 -p 5435:5432 -d \
    -e POSTGRES_PASSWORD=postgres \
    -e POSTGRES_USER=postgres \
    -e POSTGRES_DB=exampledb \
    -v pgdata:/var/lib/postgresql/data \
    postgres
```
What the command this sequence of command do is

`run` to run a countainer
`--name` flag to name the container psql1
`-p` determines the port where <host_port>:<container_port>
`-e` are the environement variables for the container and define the password, user and database name

`v` is the <name_volume>:<path inside container>

and finally the base image is the latest `postgres` image from [Docker Hub](https://hub.docker.com/_/postgres)

Before run the scrip you need to give permission to run it so run the command

`$ chmod +x commands.sh`

then `$ ./commands.sh`

If you do not have a postgres in your cache it is going to automatically download from Docker Hub and build the image then run the container

Run the following command to check it container is running

```
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
c2081d77d376        postgres            "docker-entrypoint.s…"   24 seconds ago      Up 23 seconds       0.0.0.0:5435->5432/tcp   psql1
```

you will see something like the above

Then check if the volume was created

```
$ docker volume ls
DRIVER              VOLUME NAME
local               pgdata
```

You can use any client to check it and lets do it now.

First from inside the container

`$ docker exec -it psql1 psql -p 5432  -h 0.0.0.0 -U postgres`

```
$ docker exec -it psql1 psql -h 0.0.0.0 -U postgres
psql (12.2 (Debian 12.2-2.pgdg100+1))
Type "help" for help.

postgres=#
```

Now you are inside the container using the psql client

write the command `\l` to list the database then connect to the exampledb by 

```
postgres-# \c exampledb;
You are now connected to database "exampledb" as user "postgres".
``` 
create a table

```
CREATE TABLE account(
	user_id serial PRIMARY KEY,
	username VARCHAR (50) UNIQUE NOT NULL,
	email VARCHAR (355) UNIQUE NOT NULL
);
```
`dt` to list tables

```
exampledb=# \dt
          List of relations
 Schema |  Name   | Type  |  Owner   
--------+---------+-------+----------
 public | account | table | postgres
(1 row)
```
lets add some data

```
INSERT INTO account (username, email) 
values ('John', 'john@email.com');
```
then check the data by `SELECT` statement

```
exampledb=# select * from account;
 user_id | username |     email      
---------+----------+----------------
       1 | John     | john@email.com
(1 row)
```
ok now quit `\q`

Now that the fun starts

change the .sh scrip to add more containers

```bash
docker run --name psql2 -p 5437:5432 -d \
    -e POSTGRES_PASSWORD=postgres \
    -e POSTGRES_USER=postgres \
    -e POSTGRES_DB=exampledb \
    -v pgdata:/var/lib/postgresql/data \
    postgres

 ```

 everything is the same but the name of the container and the port

 note that both container are sharing the SAME volume

 run the bash script again

 `./commands.sh`

 note that there are 2 containers running

 ```
 $ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
e504b08a8d2a        postgres            "docker-entrypoint.s…"   3 seconds ago       Up 3 seconds        0.0.0.0:5437->5432/tcp   psql2
c2081d77d376        postgres            "docker-entrypoint.s…"   18 minutes ago      Up 18 minutes       0.0.0.0:5435->5432/tcp   psql1
```

and only one volume

```
$ docker volume ls
DRIVER              VOLUME NAME
local               pgdata
```
Now lets get inside the container psql2 and connect to the database

`docker exec -it psql2 psql -p 5432  -h 0.0.0.0 -U postgres`

connect to the database `\c exampledb;`

query the account table

```
exampledb=# select * from account;
 user_id | username |     email      
---------+----------+----------------
       1 | John     | john@email.com
(1 row)
```

now lets create another record

```
INSERT INTO account (username, email) 
values ('Jane', 'jane@email.com');
```

quit `\q`

Now lets stop the containers and remove them

```
$ docker stop psql1
$ docker stop psql2
$ docker rm psql1
$ docker rm psql2
```

check if they where removed

```
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

We did not removed the volume

```
$ docker volume ls
DRIVER              VOLUME NAME
local               pgdata
```

We are now creating another container called psql3 and "connected" to the same volume

```bash
#!/bin/bash

docker run --name psql3 -p 5439:5432 -d \
    -e POSTGRES_PASSWORD=postgres \
    -e POSTGRES_USER=postgres \
    -e POSTGRES_DB=exampledb \
    -v pgdata:/var/lib/postgresql/data \
    postgres
```

Now instead of connecting inside the container we are connecting it from command line from the host machine. 
In this case you need to have either the psql cli installed in your system or use any other postgersql client tool.
I now need to use the port `-p 5439` because it is the port in the host system that is bound to the default postgres port 5432.

```
$ psql -p 5439  -h localhost -U postgres
Password for user postgres: 
psql (12.1, server 12.2 (Debian 12.2-2.pgdg100+1))
Type "help" for help.

postgres=# 

```

now it is the hour of reckoning and lets check if we can access the data

```
postgres=# \c exampledb;
psql (12.1, server 12.2 (Debian 12.2-2.pgdg100+1))
You are now connected to database "exampledb" as user "postgres".
exampledb=# \dt
          List of relations
 Schema |  Name   | Type  |  Owner   
--------+---------+-------+----------
 public | account | table | postgres
(1 row)

exampledb=# select * from account;
 user_id | username |     email      
---------+----------+----------------
       1 | John     | john@email.com
       2 | Jane     | jane@email.com
(2 rows)
```

AMAZING 🤯! THERE YOU HAVE IT!

Isn't cool?

The data persisted in the volume and new containers could easily connect to it. Imagine an environment of continued development and the possibilities and flexibility that it brings.

to clean up your system we can

`\q` get out of the psql cli tool.

Stop and remove the containers and volume.

```
$ docker stop psql3
$ docker rm psql3
$ docker volume rm pgdata
$ docker volume ls
```

Did not do the example with bind mount however the principle is the same and the documentation is [here](https://docs.docker.com/storage/bind-mounts/)

If you get to here thanks for reading 😀.








