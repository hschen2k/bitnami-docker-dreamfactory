
# What is DreamFactory?

> DreamFactory is an open source REST API for mobile enterprise application developers. Add a REST API to any backend system. Services include SQL, NoSQL, BLOB, email, users, roles, security, and integration. Whether you’re building a native or web-based app, DreamFactory developers can focus on creating great front-ends to their apps, while leaving all the backend work to DreamFactory.

https://www.dreamfactory.com/

# TL;DR;

## Docker Compose

```bash
$ curl -sSL https://raw.githubusercontent.com/bitnami/bitnami-docker-dreamfactory/master/docker-compose.yml > docker-compose.yml
$ docker-compose up -d
```

# Why use Bitnami Images?

* Bitnami closely tracks upstream source changes and promptly publishes new versions of this image using our automated systems.
* With Bitnami images the latest bug fixes and features are available as soon as possible.
* Bitnami containers, virtual machines and cloud images use the same components and configuration approach - making it easy to switch between formats based on your project needs.
* All our images are based on [minideb](https://github.com/bitnami/minideb) a minimalist Debian based container image which gives you a small base container image and the familiarity of a leading linux distribution.
* All Bitnami images available in Docker Hub are signed with [Docker Content Trust (DTC)](https://docs.docker.com/engine/security/trust/content_trust/). You can use `DOCKER_CONTENT_TRUST=1` to verify the integrity of the images.
* Bitnami container images are released daily with the latest distribution packages available.


> This [CVE scan report](https://quay.io/repository/bitnami/dreamfactory?tab=tags) contains a security report with all open CVEs. To get the list of actionable security issues, find the "latest" tag, click the vulnerability report link under the corresponding "Security scan" field and then select the "Only show fixable" filter on the next page.

# How to deploy DreamFactory in Kubernetes?

You can find an example for testing in the file `test.yaml`. To launch this sample file run:

```bash
$ kubectl apply -f test.yaml
```

> NOTE: If you are pulling from a private containers registry, replace the image name with the full URL to the docker image. E.g.
>
> - image: 'your-registry/image-name:your-version'

# Supported tags and respective `Dockerfile` links

> NOTE: Debian 8 images have been deprecated in favor of Debian 9 images. Bitnami will not longer publish new Docker images based on Debian 8.

Learn more about the Bitnami tagging policy and the difference between rolling tags and immutable tags [in our documentation page](https://docs.bitnami.com/containers/how-to/understand-rolling-tags-containers/).


* [`3-ol-7`, `3.0.1-ol-7-r123` (3/ol-7/Dockerfile)](https://github.com/bitnami/bitnami-docker-dreamfactory/blob/3.0.1-ol-7-r123/3/ol-7/Dockerfile)
* [`3-debian-9`, `3.0.1-debian-9-r105`, `3`, `3.0.1`, `3.0.1-r105`, `latest` (3/debian-9/Dockerfile)](https://github.com/bitnami/bitnami-docker-dreamfactory/blob/3.0.1-debian-9-r105/3/debian-9/Dockerfile)

Subscribe to project updates by watching the [bitnami/dreamfactory GitHub repo](https://github.com/bitnami/bitnami-docker-dreamfactory).


# Prerequisites

To run this application you need [Docker Engine](https://www.docker.com/products/docker-engine) >= `1.10.0`. [Docker Compose](https://www.docker.com/products/docker-compose) is recommended with a version `1.6.0` or later.

# How to use this image

DreamFactory requires access to a MySQL database or MariaDB database to store information. We'll use our very own [MariaDB image](https://www.github.com/bitnami/bitnami-docker-mariadb) for the database requirements. It also uses our [MongoDB image] (https://www.github.com/bitnami/bitnami-docker-mongodb) and [Redis image] (https://www.github.com/bitnami/bitnami-docker-redis).

## Using Docker Compose

The main folder of this repository contains a functional [`docker-compose.yml`](https://github.com/bitnami/bitnami-docker-dreamfactory/blob/master/docker-compose.yml) file. Run the application using it as shown below:

```bash
$ curl -sSL https://raw.githubusercontent.com/bitnami/bitnami-docker-dreamfactory/master/docker-compose.yml > docker-compose.yml
$ docker-compose up -d
```

## Using the Docker Command Line

If you want to run the application manually instead of using `docker-compose`, these are the basic steps you need to run:

1. Create a network

```bash
$ docker network create dreamfactory-tier
```

2. Create a volume for MariaDB persistence and create a MariaDB container

```bash
$ docker volume create --name mariadb_data
$ docker run -d --name mariadb -e ALLOW_EMPTY_PASSWORD=yes\
  --net dreamfactory-tier \
  --volume mariadb_data:/bitnami \
  bitnami/mariadb:latest
```

3. Create a volume for MongoDB persistence and create a MongoDB container

```bash
$ docker volume create --name mongodb_data
$ docker run -d --name mongodb \
  --net dreamfactory-tier \
  --volume mongodb_data:/bitnami \
  bitnami/mongodb:latest
```

4. Create a volume for Redis persistence and create a Redis container

```bash
$ docker volume create --name redis_data
$ docker run -d --name redis -e ALLOW_EMPTY_PASSWORD=yes \
  --net dreamfactory-tier \
  --volume redis_data:/bitnami \
  bitnami/redis:latest
```

5. Create volumes for DreamFactory persistence and launch the container

```bash
$ docker volume create --name dreamfactory_data
$ docker run -d --name dreamfactory -p 80:80 -p 443:443 \
  --net dreamfactory-tier \
  --volume dreamfactory_data:/bitnami \
  bitnami/dreamfactory:latest
```

Access your application at http://your-ip/

## Persisting your application

If you remove the container all your data and configurations will be lost, and the next time you run the image the database will be reinitialized. To avoid this loss of data, you should mount a volume that will persist even after the container is removed.

For persistence you should mount a volume at the `/bitnami` path. Additionally you should mount a volume for persistence of the [MariaDB](https://github.com/bitnami/bitnami-docker-mariadb#persisting-your-database), [MongoDB](https://github.com/bitnami/bitnami-docker-mongodb#persisting-your-database) and [Redis](https://github.com/bitnami/bitnami-docker-redis#persisting-your-database) data.

The above examples define docker volumes namely `mariadb_data`, `mongodb_data`, `redis_data` and `dreamfactory_data`. The DreamFactory application state will persist as long as these volumes are not removed.

To avoid inadvertent removal of these volumes you can [mount host directories as data volumes](https://docs.docker.com/engine/tutorials/dockervolumes/). Alternatively you can make use of volume plugins to host the volume data.

### Mount host directories as data volumes with Docker Compose

This requires a minor change to the [`docker-compose.yml`](https://github.com/bitnami/bitnami-docker-dreamfactory/blob/master/docker-compose.yml) file present in this repository:

```yaml
services:
  mariadb:
  ...
    volumes:
      - /path/to/mariadb-persistence:/bitnami
  ...
  mongodb:
  ...
    volumes:
      - '/path/to/mongodb-persistence:/bitnami'
  ...
  redis:
  ...
    volumes:
      - '/path/to/redis-persistence:/bitnami'
  ...
  dreamfactory:
  ...
    volumes:
      - /path/to/dreamfactory-persistence:/bitnami
  ...
```

### Mount host directories as data volumes using the Docker command line

1. Create a network (if it does not exist)

```bash
$ docker network create dreamfactory-tier
```

2. Create a MariaDB container with host volume

```bash
$ docker run -d --name mariadb -e ALLOW_EMPTY_PASSWORD=yes \
  --net dreamfactory-tier \
  --volume /path/to/mariadb-persistence:/bitnami \
  bitnami/mariadb:latest
```

3. Create a MongoDB container with host volume

```bash
$ docker run -d --name mongodb \
  --net dreamfactory-tier \
  --volume /path/to/mongodb-persistence:/bitnami \
  bitnami/mongodb:latest
```


4. Create a Redis container with host volume

```bash
$ docker run -d --name redis -e ALLOW_EMPTY_PASSWORD=yes \
  --net dreamfactory-tier \
  --volume /path/to/redis-persistence:/bitnami \
  bitnami/redis:latest
```

5. Create the DreamFactory the container with host volumes

```bash
$ docker run -d --name dreamfactory -p 80:80 -p 443:443 \
  --net dreamfactory-tier \
  --volume /path/to/dreamfactory-persistence:/bitnami \
  bitnami/dreamfactory:latest
```

# Upgrading DreamFactory

Bitnami provides up-to-date versions of MariaDB, MongoDB, Redis and DreamFactory, including security patches, soon after they are made upstream. We recommend that you follow these steps to upgrade your container. We will cover here the upgrade of the DreamFactory container. For the MariaDB upgrade see https://github.com/bitnami/bitnami-docker-mariadb/blob/master/README.md#upgrade-this-image. For the MongoDB upgrade see https://github.com/bitnami/bitnami-docker-mongodb/blob/master/README.md#upgrade-this-image. For the Redis upgrade see https://github.com/bitnami/bitnami-docker-redis/blob/master/README.md#upgrade-this-image

The `bitnami/dreamfactory:latest` tag always points to the most recent release. To get the most recent release you can simple repull the `latest` tag from the Docker Hub with `docker pull bitnami/dreamfactory:latest`. However it is recommended to use [tagged versions](https://hub.docker.com/r/bitnami/dreamfactory/tags/).

1. Get the updated images:

  ```bash
  $ docker pull bitnami/dreamfactory:latest
  ```

2. Stop your container

 * For docker-compose: `$ docker-compose stop dreamfactory`
 * For manual execution: `$ docker stop dreamfactory`

3. Take a snapshot of the application state

```bash
$ rsync -a /path/to/dreamfactory-persistence /path/to/dreamfactory-persistence.bkp.$(date +%Y%m%d-%H.%M.%S)
```

Additionally, snapshot the [MariaDB](https://github.com/bitnami/bitnami-docker-mariadb#step-2-stop-and-backup-the-currently-running-container), [MongoDB](https://github.com/bitnami/bitnami-docker-mongodb#step-2-stop-and-backup-the-currently-running-container) and [Redis](https://github.com/bitnami/bitnami-docker-redis#step-2-stop-and-backup-the-currently-running-container) data.

You can use these snapshots to restore the application state should the upgrade fail.

4. Remove the currently running container

 * For docker-compose: `$ docker-compose rm -v dreamfactory`
 * For manual execution: `$ docker rm -v dreamfactory`

5. Run the new image

 * For docker-compose: `$ docker-compose up dreamfactory`
 * For manual execution ([mount](#mount-persistent-folders-manually) the directories if needed): `docker run --name dreamfactory bitnami/dreamfactory:latest`

# Configuration

## Environment variables

The DreamFactory instance can be customized by specifying environment variables on the first run. The following environment values are provided to custom DreamFactory:

- `MARIADB_USER`: Root user for the MariaDB database. Default: **root**
- `MARIADB_PASSWORD`: Root password for the MariaDB.
- `MARIADB_HOST`: Hostname for MariaDB server. Default: **mariadb**
- `MARIADB_PORT_NUMBER`: Port used by MariaDB server. Default: **3306**
- `MONGODB_HOST`: Hostname for Mongodb server. Default: **mongodb**
- `MONGODB_PORT_NUMBER`: Port used by Mongodb server. Default: **27017**
- `REDIS_HOST`: Hostname for Redis. Default: **redis**
- `REDIS_PORT_NUMBER`: Port used by Redis. Default: **6379**
- `REDIS_PASSWORD`: Password for Redis.
- `SMTP_HOST`: Hostname for the SMTP server (necessary for sending e-mails from the application).
- `SMTP_PORT`: Port for the SMTP server.
- `SMTP_PROTOCOL`: Secure connection protocol to use for SMTP [tls, ssl, none].
- `SMTP_USER`: Username for the SMTP server.
- `SMTP_PASSWORD`: SMTP account password.

### Specifying Environment variables using Docker Compose

This requires a minor change to the [`docker-compose.yml`](https://github.com/bitnami/bitnami-docker-dreamfactory/blob/master/docker-compose.yml) file present in this repository:

```yaml
dreamfactory:
  ...
  environment:
    - SMTP_HOST=smtp.gmail.com
    - SMTP_PORT=587
    - SMTP_USER=your_email@gmail.com
    - SMTP_PASSWORD=your_password
    - SMTP_PROTOCOL=tls
  ...
```

### Specifying environment variables on the Docker command line

```bash
$ docker run -d --name dreamfactory -p 80:80 -p 443:443 \
  --net dreamfactory-tier \
  --env SMTP_HOST=smtp.gmail.com --env SMTP_PORT=587 \
  --env SMTP_USER=your_email@gmail.com --env SMTP_PASSWORD=your_password \
  --volume dreamfactory_data:/bitnami \
  bitnami/dreamfactory:latest
```

# Customize this image

The Bitnami DreamFactory Docker image is designed to be extended so it can be used as the base image for your custom web applications.

## Extend this image

Before extending this image, please note there are certain configuration settings you can modify using the original image:

- Settings that can be adapted using environment variables. For instance, you can change the ports used by Apache for HTTP and HTTPS, by setting the environment variables `APACHE_HTTP_PORT_NUMBER` and `APACHE_HTTPS_PORT_NUMBER` respectively.
- [Adding custom virtual hosts](https://github.com/bitnami/bitnami-docker-apache#adding-custom-virtual-hosts).
- [Replacing the 'httpd.conf' file](https://github.com/bitnami/bitnami-docker-apache#full-configuration).
- [Using custom SSL certificates](https://github.com/bitnami/bitnami-docker-apache#using-custom-ssl-certificates).

If your desired customizations cannot be covered using the methods mentioned above, extend the image. To do so, create your own image using a Dockerfile with the format below:

```Dockerfile
FROM bitnami/dreamfactory
## Put your customizations below
...
```

Here is an example of extending the image with the following modifications:

- Install the `vim` editor
- Modify the Apache configuration file
- Modify the ports used by Apache

```Dockerfile
FROM bitnami/dreamfactory
LABEL maintainer "Bitnami <containers@bitnami.com>"

## Install 'vim'
RUN install_packages vim

## Enable mod_ratelimit module
RUN sed -i -r 's/#LoadModule ratelimit_module/LoadModule ratelimit_module/' /opt/bitnami/apache/conf/httpd.conf

## Modify the ports used by Apache by default
# It is also possible to change these environment variables at runtime
ENV APACHE_HTTP_PORT_NUMBER=8181 
ENV APACHE_HTTPS_PORT_NUMBER=8143
EXPOSE 8181 8143
```

Based on the extended image, you can use a Docker Compose file like the one below to add other features:

```yaml
version: '2'

services:
  mariadb:
    image: 'bitnami/mariadb:10.3'
    environment:
      - ALLOW_EMPTY_PASSWORD=yes
    volumes:
      - 'mariadb_data:/bitnami'
  redis:
    image: 'bitnami/redis:5.0'
    environment:
      - ALLOW_EMPTY_PASSWORD=yes
    volumes:
      - 'redis_data:/bitnami'
  mongodb:
    image: 'bitnami/mongodb:4.0'
    volumes:
      - 'mongodb_data:/bitnami'
  dreamfactory:
    build: .
    ports:
      - '80:8181'
      - '443:8143'
    depends_on:
      - mariadb
      - redis
      - mongodb
    volumes:
      - 'dreamfactory_data:/bitnami'
volumes:
  mariadb_data:
    driver: local
  redis_data:
    driver: local
  mongodb_data:
    driver: local
  dreamfactory_data:
    driver: local
```

# Notable Changes

## 2.14.1-debian-9-r195 and 2.14.1-ol-7-r204

- This image has been adapted so it's easier to customize. See the [Customize this image](#customize-this-image) section for more information.
- The Apache configuration volume (`/bitnami/apache`) has been deprecated, and support for this feature will be dropped in the near future. Until then, the container will enable the Apache configuration from that volume if it exists. By default, and if the configuration volume does not exist, the configuration files will be regenerated each time the container is created. Users wanting to apply custom Apache configuration files are advised to mount a volume for the configuration at `/opt/bitnami/apache/conf`, or mount specific configuration files individually.
- The PHP configuration volume (`/bitnami/php`) has been deprecated, and support for this feature will be dropped in the near future. Until then, the container will enable the PHP configuration from that volume if it exists. By default, and if the configuration volume does not exist, the configuration files will be regenerated each time the container is created. Users wanting to apply custom PHP configuration files are advised to mount a volume for the configuration at `/opt/bitnami/php/conf`, or mount specific configuration files individually. 
- Enabling custom Apache certificates by placing them at `/opt/bitnami/apache/certs` has been deprecated, and support for this functionality will be dropped in the near future. Users wanting to enable custom certificates are advised to mount their certificate files on top of the preconfigured ones at `/certs`. 

# Contributing

We'd love for you to contribute to this container. You can request new features by creating an [issue](https://github.com/bitnami/bitnami-docker-dreamfactory/issues), or submit a [pull request](https://github.com/bitnami/bitnami-docker-dreamfactory/pulls) with your contribution.

# Issues

If you encountered a problem running this container, you can file an [issue](https://github.com/bitnami/bitnami-docker-dreamfactory/issues). For us to provide better support, be sure to include the following information in your issue:

- Host OS and version
- Docker version (`docker version`)
- Output of `docker info`
- Version of this container (`echo $BITNAMI_IMAGE_VERSION` inside the container)
- The command you used to run the container, and any relevant output you saw (masking any sensitive information)

# License

Copyright 2019 Bitnami

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

  <http://www.apache.org/licenses/LICENSE-2.0>

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
