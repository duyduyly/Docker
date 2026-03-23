# Docker Tutorial

## DockerFile
#### Read Here: [DockerFile](lessons/DockerFile.md)


--------------
<br/>

## Docker Compose
#### Read Here: [Docker Compose.md](lessons/Docker%20Compose.md)

-------------
<br/>

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