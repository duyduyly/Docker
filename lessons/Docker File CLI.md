# Docker File CLI Commands

#### [Home](../Docker.md) > `DockerFile Cmd`

## 1. Build an Image from Dockerfile

```bash
docker build -t my-app .
```

- `-t my-app`: tags the image with the name `my-app`
- `.`: specifies the build context (current directory)

---

## 2. List Images

```bash
docker images
```
* Shows all available Docker images.

---

## 3. Run a Container

```bash
docker run -d -p 8080:80 --name my-container my-app
```

* Starts a container from the image.
* `-d`: run in background
* `-p 8080:80`: map ports
* `--name`: make name for container

---

## 4. Execute Commands Inside Container

```bash
docker exec -it my-container bash
```

* Access the container terminal.
* Run container in interactive mode (`-it`).
  * run 
  * debug
  * check files

Example:

```bash
docker exec -it my-container composer install
```

---

## 5. Stop / Start Container

```bash
docker stop my-container
docker start my-container
```

---

## 6. Remove Container

```bash
docker rm my-container
```

---

## 7. Remove Image

```bash
docker rmi my-app
```

---

## 8. View Logs

```bash
docker logs my-container
```

---

## 9. Rebuild After Changes

```bash
docker build -t my-app .
docker rm -f my-container
docker run -d -p 8080:80 --name my-container my-app
```

---

## 10. Using Docker Compose (Recommended)

```bash
docker-compose up -d --build
```

* Builds and runs everything defined in `docker-compose.yml`.

---

# ✅ Typical Workflow

```text
dockerfile -> build image -> run container -> execute commands (if needed)

#case update code or configuration:

remove container(rm) -> rebuild image(build) -> run container
```

