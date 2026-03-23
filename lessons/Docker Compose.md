# Docker Compose

#### [Home](../Docker.md) > `DockerFile Cmd`

## Keys
- [Docker Compose](#docker-compose)
- [Key Components in Docker Compose](#key-components-in-docker-compose)

-----------
<br/>

## Docker Compose
- `Docker Compose` is a tool for defining and running multi-container Docker applications.
- It uses a YAML file (`docker-compose.yml`) to configure the application’s services, networks, and volumes.

- Example:
    - 1 container PHP + Apache
    - 1 container MySQL
    - 1 container Redis
- Instead of running each container separately.
```text
docker run ...
docker run ...
docker run ...
```

- you can define them in a `docker-compose.yml` file and start them together.
```text
docker compose up
```

#
### Key Components in Docker Compose

* [version](#1-version) : defines the syntax version of docker-compose
* [services](#2-services) : defines containers in the system
* [image / build](#3-image--build) : specifies how to create container images
* [ports](#4-ports) : maps host ports to container ports
* [volumes](#5-volumes) : mounts data between host and container
* [environment / env_file](#6-environment--env_file) : sets environment variables
* [depends_on](#7-depends_on) : defines startup order between services
* [networks](#8-networks) : manages communication between containers

---

## 1. version

```yaml
version: "3.8"
```

* Defines docker-compose file format version
* New Docker versions can ignore this field
* Helps ensure compatibility

---

## 2. services

```yaml
services:
  web:
  db:
```

* Core component of docker-compose
* Each service = one container
* Example:

    * `web` → PHP/Yii app
    * `db` → MySQL database

---

## 3. image / build

```yaml
web:
  build: .

db:
  image: mysql:8
```

* `build`:

    * Builds image from Dockerfile
    * Used for custom environments
* `image`:

    * Uses pre-built image from Docker Hub
    * Faster, no build step

---

## 4. ports

```yaml
ports:
  - "8080:80"
```

* Format: `host_port:container_port`
* Access app via `localhost:8080`
* Container runs internally on port 80

---

## 5. volumes

```yaml
volumes:
  - .:/var/www/html
```

* Syncs files between host and container
* Useful for development (live reload)
* Also used for persistent data (e.g., database)

---

## 6. environment / env_file

```yaml
environment:
  DB_HOST: db

env_file:
  - .env
```

* `environment`:

    * Define variables directly
* `env_file`:

    * Load variables from `.env` file
* Used by application (e.g., Yii config)

---

## 7. depends_on

```yaml
depends_on:
  - db
```

* Ensures service starts after another
* Does NOT guarantee the service is ready
* Often combined with wait-for scripts

---

## 8. networks

```yaml
networks:
  - app-network
```

* Allows containers to communicate
* Services can use names as host:

    * `DB_HOST=db`
* Default network is auto-created

---

# 🚀 Final Takeaway

- Example `docker-compose.yml` for Yii2 app with MySQL and Redis:

```yaml
version: "3.8"

services:
  web:
    build: .
    ports:
      - "8080:80"
    volumes:
      - .:/var/www/html
    env_file:
      - .env
    depends_on:
      - db

  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      MYSQL_DATABASE: yii_db
    volumes:
      - db_data:/var/lib/mysql
    ports:
      - "3307:3306"

volumes:
  db_data:
```

```text
Docker Compose = define + run multi-container systems
```

* `services` → containers
* `build/image` → how to create them
* `ports` → access
* `volumes` → data
* `environment` → config

---

## Docker Compose Commands

* [up](#1-up) : build and start all services
* [down](#2-down) : stop and remove containers, networks
* [build](#3-build) : build images from Dockerfile
* [ps](#4-ps) : list running containers
* [logs](#5-logs) : view container logs
* [exec](#6-exec) : run command inside a container
* [run](#7-run) : run one-off command in a new container
* [restart](#8-restart) : restart services
* [stop](#9-stop) : stop running containers

---

## 1. up

```bash id="2y7w5q"
docker compose up
docker compose up -d
docker compose up --build
```

* Starts all services defined in `docker-compose.yml`
* `-d` → run in background (detached mode)
* `--build` → rebuild images before starting
* Automatically creates network + containers

---

## 2. down

```bash id="q9rlfp"
docker compose down
docker compose down -v
```

* Stops and removes containers
* Removes default network
* `-v` → also remove volumes (⚠️ deletes data)

---

## 3. build

```bash id="2zaj0d"
docker compose build
docker compose build web
```

* Builds images from Dockerfile
* Can target a specific service (`web`)
* Useful when changing dependencies

---

## 4. ps

```bash id="o5a6l6"
docker compose ps
```

* Lists running containers
* Shows:

    * container name
    * status
    * ports

---

## 5. logs

```bash id="ynk41d"
docker compose logs
docker compose logs -f
docker compose logs web
```

* Shows logs from containers
* `-f` → follow logs (real-time)
* Can filter by service (`web`)

---

## 6. exec

```bash id="q4ylw0"
docker compose exec web bash
docker compose exec web php yii migrate
```

* Runs command inside a running container
* Common uses:

    * access shell (`bash`)
    * run Yii commands

---

## 7. run

```bash id="a1kh28"
docker compose run web php yii migrate
```

* Runs a one-time command
* Creates a new temporary container
* Does NOT require container to be running

---

## 8. restart

```bash id="o0q1p3"
docker compose restart
docker compose restart web
```

* Restarts all or specific services
* Useful after config changes

---

## 9. stop

```bash id="v9h6tu"
docker compose stop
```

* Stops containers without removing them
* Faster than `down` when restarting later

---

### Workflow

```bash id="ay3s3h"
# 1. Start system
docker compose up -d --build

# 2. Check running containers
docker compose ps

# 3. View logs
docker compose logs -f

# 4. Run Yii migration
docker compose exec web php yii migrate

# 5. Stop system
docker compose down
```

---

# 🎯 Final Takeaway
```text id="e5b6c6"
docker compose up → start system
docker compose exec → interact with container
docker compose down → stop system
```
