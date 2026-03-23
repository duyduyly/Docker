# Docker Tutorial

## DockerFile
- `DockerFile` is file containing statements to build a Docker image. (same name as the tool)

### Dockerfile Commands
- Read here: [Docker File CLI Commands](lessons/Docker%20File%20CLI.md)

### Components in DockerFile
- [FROM](#1-from-base-image) : Base image (e.g., `php:8.2-fpm`)
- [RUN](#4-run) : Commands to install software (e.g., `apt-get install -y ...
- [COPY](#3-copy): Copy files from host to image (e.g., `COPY . /var/www/html`)
- [WORKDIR](#2-workdir): Set working directory (e.g., `WORKDIR /var/www/html
- [EXPOSE](#7-expose): Expose ports (e.g., `EXPOSE 80`)
- [CMD Or ENTRYPOINT](#5-cmd-or-entrypoint): Command to run when container starts (e.g., `CMD ["php-fpm"]`)
- [ENV](#6-env): Set environment variables (e.g., `ENV APP_ENV=production`)

#
#### 1. `FROM` base image
- Must be the first instruction in a Dockerfile.
- Defines original image to build upon.

**Example**
```dockerfile
FROM php:8.4-apache  
# or
FROM Java:17
```

#
#### 2. WORKDIR
- Folder where commands will be `executed`.
- work after `COPY` -> copy here.
- `RUN` -> run here.
- Thinking: “Where am I in the container’s file system?”

```dockerfile
WORKDIR /var/www/html
```

#
#### 3. COPY
- Copy files from your host to image.
- thinking: “Move my code into”



```dockerfile
COPY . /var/www/html
```

#
#### 4. RUN
- Run commands to install software or configure the image.
- Use for:
  - installing dependencies (e.g., `apt-get install -y ...`)
  - install packages (e.g., `composer install`)
  - build dependencies (e.g., `npm install`)
- thinking: “What do I need to set up my environment?”

```dockerfile
RUN composer install
```

#
#### 5. CMD or ENTRYPOINT
- Define the command to run when the `container starts`.
- Entry point: same as CMD but can’t be overridden when running container.
- thinking: “What should my container do when it starts?”

```dockerfile
CMD ["apache2-foreground"]

#Or -> run php index.php when container starts
ENTRYPOINT ["php", "index.php"]
```
 
#
##### Tip for entry point
entrypoint.sh
- Use when you need to run multiple commands before starting the main process (e.g., initializing Yii, running migrations).
```sh
#!/bin/sh

echo "Running Yii init..."

php init --env=Production --overwrite=All

echo "Running migrations..."
php yii migrate --interactive=0

echo "Starting Apache..."
exec apache2-foreground
```

after that, in Dockerfile:
- we can copy this script to the image and set it as the entry point. This way, when the container starts, it will run the initialization and migration commands before starting Apache.
```dockerfile
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
```

#
#### 6. ENV
- Set environment variables in the container.
- Useful for configuration (e.g., database credentials, app environment).
- thinking: “What configuration does my app need?”


```dockerfile
ENV APP_ENV=production
```

#
#### 7. EXPOSE
- Inform Docker that the container listens on specified network ports at runtime.
- Does not actually publish the port (use `docker run -p` or `docker compose
- thinking: “What ports does my app use?”

```dockerfile
EXPOSE 80
```

#
#### 8. ADD
- Similar to `COPY` but with extra features:
  - Can extract tar files (e.g., `ADD source.tar.gz /app`)
  - Can fetch files from URLs (e.g., `ADD https://example.com/file.txt /app`)
- Use `COPY` for simple file copying and `ADD` when you need its additional features
- thinking: “Do I need to extract or fetch files?”

```dockerfile
ADD source.tar.gz /app
```

#
#### 9. ARG
- Define build-time variables that can be passed during the build process.
- Useful for customizing the build without hardcoding values in the Dockerfile.
- thinking: “What parameters do I want to customize during build?”
```dockerfile
ARG APP_VERSION=1.0.0
```

##
#### Full Example with Yii2

enpoint.sh
```shell
echo "Running Yii init..."

php init --env=Production --overwrite=All

echo "Running migrations..."
php yii migrate --interactive=0

echo "Starting Apache..."
exec apache2-foreground
```

```dockerfile
FROM php:8.2-apache

WORKDIR /var/www/html

RUN apt-get update && apt-get install -y \
    git unzip curl

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

COPY . .

RUN composer install

RUN docker-php-ext-install pdo pdo_mysql

EXPOSE 80

COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
```

--------------
<br/>

## Docker Compose


## DockerFile And Docker Compose
### DockerFile
- Define how to build a Docker image.
- Contains instructions like:
  - Install software (PHP, Composer…)
  - Copy source code
  - Configure environment
  - Used with: `docker build`
- 👉 Think: “`Recipe` to create an image”

### Docker Compose
- Define how to run containers.
- Manages:
  - Multiple services (web, database…)
  - Ports, volumes, environment variables
  - Used with: docker compose up
- 👉 Think: “System `blueprint` to run everything”

### Relationship
```cmd
Dockerfile → builds image → Docker Compose runs containers
```

- Note
  - `Dockerfile` ≠ run container (only builds image)
  - `Compose` can run without `Dockerfile` (if using ready-made images)
  - For custom setup (like Yii + PHP extensions) → you NEED `Dockerfile`