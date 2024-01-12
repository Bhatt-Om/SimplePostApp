# Docker Integration 🐳

## Disclaimer :
This documentation is based on my current understanding of Docker, and I acknowledge that I am new to its concepts. Please approach the information provided with caution and consider it as a representation of my current knowledge, subject to updates and improvements as I continue to explore and learn more about Docker.

## Introduction to Docker

**Q> What is Docker?**
  Docker is a platform that enables developers to automate the deployment of applications inside lightweight, portable containers. These containers encapsulate the application and its dependencies, ensuring consistent and reliable execution across various environments.

**Docker Objects :**
Docker introduces several key objects that are crucial for understanding its functionality:

Image: A lightweight, standalone, and executable package that includes everything needed to run a piece of software, including the code, runtime, libraries, and system tools.

Container: An instance of a Docker image, running as a process isolated from the host system. Containers are portable and can run consistently on any environment that supports Docker.

Dockerfile: A script containing instructions for building a Docker image. It specifies the base image, additional dependencies, and configuration for the application.

Registry: A centralized repository for storing and distributing Docker images. The Docker Hub is a popular public registry, but private registries can also be used for secure image storage.

Compose: A tool for defining and running multi-container Docker applications. Compose uses a YAML file to configure the services, networks, and volumes, simplifying the deployment of complex applications.

These fundamental Docker objects empower developers to create, deploy, and manage applications efficiently in containerized environments.

**DockerFile :**
```
FROM ruby:3.2.2-alpine

ENV RAILS_ROOT /app
ENV LANG C.UTF-8

WORKDIR $RAILS_ROOT

RUN apk update && \
    apk add --no-cache build-base sqlite-dev nodejs postgresql-dev
    

COPY . .
RUN gem install bundler && bundle install --jobs 20 --retry 5


ENTRYPOINT [ "/app/entrypoint/docker-entrypoint.sh" ]
CMD ["server"]
```

FROM ruby:3.2.2-alpine:

Sets the base image to Ruby version 3.2.2-alpine, which is a lightweight version of the Alpine Linux distribution with Ruby pre-installed.
ENV RAILS_ROOT /app:

Defines an environment variable RAILS_ROOT with the value "/app". This will be the working directory for the subsequent commands.
ENV LANG C.UTF-8:

Sets the default language to UTF-8.
WORKDIR $RAILS_ROOT:

Changes the working directory to the value of RAILS_ROOT (which is "/app").
RUN apk update && :

Updates the package index (apk update) and continues to the next command using the backslash.
apk add --no-cache build-base sqlite-dev nodejs postgresql-dev:

Installs the required dependencies for building a Ruby on Rails application, including build tools (build-base), SQLite development libraries (sqlite-dev), Node.js, and PostgreSQL development libraries (postgresql-dev).
COPY . .:

Copies all files from the current directory (the context where the docker build command is executed) to the /app directory in the Docker image.
RUN gem install bundler && bundle install --jobs 20 --retry 5:

Installs the Bundler gem and then installs the project dependencies using Bundler. The --jobs 20 and --retry 5 flags optimize the installation process by running multiple jobs in parallel and retrying failed operations.
ENTRYPOINT [ "/app/entrypoint/docker-entrypoint.sh" ]:

Sets the default entry point script for the container. It specifies the location of the script (/app/entrypoint/docker-entrypoint.sh), which might include setup tasks before running the main application.
CMD ["server"]:

Specifies the default command to be executed when the container starts. In this case, it starts the Rails server.

**Docker-Compose :**
```
version: '3.8'

services:
  db:
    image: postgres
    volumes:
      - "dbdata:/var/lib/postgresql/data"
    environment:
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"
  
  web:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - ".:/app"
    depends_on:
      - db
    environment:
      - DATABASE_URL=postgres://postgres:password@db:5432/postgres

volumes:
  dbdata:
```

version: '3.8': Specifies the version of the Docker Compose file format.

services: Defines the services that make up the application.

db:

image: postgres: Specifies the PostgreSQL Docker image to be used for the database service.
volumes:: Maps the dbdata volume to the /var/lib/postgresql/data directory in the container, preserving the database data.
environment:: Sets the environment variable POSTGRES_PASSWORD to "password" for PostgreSQL authentication.
ports:: Maps port 5432 on the host to port 5432 on the container, allowing connections to the PostgreSQL database.

web:

build: .: Builds the Docker image for the web service using the local context (the current directory).
ports:: Maps port 3000 on the host to port 3000 on the container, enabling access to the web application.
volumes:: Mounts the current directory to /app in the container, allowing for live code updates.
depends_on:: Ensures that the db service is started before the web service.
environment:: Sets the environment variable DATABASE_URL to connect to the PostgreSQL database.
volumes:: Defines a named volume dbdata, which is used to persist the data of the PostgreSQL database.

**Docker Coammnds:**
Here are some commonly used Docker commands:

docker images: List all Docker images.

docker ps: List all running Docker containers.

docker build: Build a Docker image from a Dockerfile.

docker-compose build: Build or rebuild services defined in a Docker Compose file.

docker-compose up: Start services defined in a Docker Compose file.


**Docker Tag, Push, and Pull Commands :**
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]:
Tags a local image with a new image name or version.

docker push IMAGE_NAME[:TAG]:
Pushes a Docker image or a tagged version to a remote registry.

docker pull IMAGE_NAME[:TAG]:
Pulls a Docker image or a specific tagged version from a remote registry.
These commands are crucial for versioning, distributing, and sharing Docker images across different environments and systems.

**Docker Network :**
Docker Network allows containers to communicate with each other within the same or different hosts, facilitating seamless connectivity
command :
```
docker network create my_network
docker run --name db --network my_network -e POSTGRES_PASSWORD=password -d postgres
docker run --name my_app_container --network my_network -p 3000:3000 -e DATABASE_URL=postgres://postgres:password@db:5432/postgres -d bhattom09/simple_post-web:1.0
```
#Thank You. 🙏
