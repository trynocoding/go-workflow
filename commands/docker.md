---
name: docker
description: "Build, run, and manage Docker containers for Go applications. Usage: /go-workflow:docker <action> [options]. Actions: build, run, up, stop, push, clean."
argument-hint: "<action> [options]"
disable-model-invocation: true
allowed-tools:
  - Bash
  - Read
  - Glob
---

# Docker Deployment for Go Projects

Load and follow the `go-code-review` skill if Dockerfile or docker-compose.yml exists.

## Available Actions

### 1. build - Build Docker image
```
/go-workflow:docker build [tag]
```
Builds the Docker image. Optional tag (default: current git branch name).

### 2. run - Run container (one-shot)
```
/go-workflow:docker run [env vars]
```
Runs the container once and removes it after. Useful for testing.

### 3. up - Build and run with docker-compose
```
/go-workflow:docker up
```
Builds and starts all services defined in docker-compose.yml.

### 4. stop - Stop running containers
```
/go-workflow:docker stop
```
Stops all running containers.

### 5. push - Push image to registry
```
/go-workflow:docker push [tag]
```
Pushes the image to Docker registry. Requires tag.

### 6. clean - Remove unused resources
```
/go-workflow:docker clean
```
Removes dangling images and stopped containers.

---

## Execution

Processing action: $ARGUMENTS

!`echo "$ARGUMENTS" | cut -d' ' -f1`

### Current project context:
```
Project: !`basename $(git rev-parse --show-toplevel) 2>/dev/null || basename "$PWD"`
Branch: !`git branch --show-current 2>/dev/null || echo "N/A"`
```

### Checking for Docker files:

```
!`ls -la Dockerfile* docker-compose* 2>/dev/null || echo "No Docker files found"`
```

---

## Action: $ARGUMENTS

Based on the action, execute the appropriate Docker command:

### build
1. Detect project name from go.mod or directory name
2. Detect current branch for default tag
3. Run: `docker build -t <image>:<tag> .`
4. Show image ID on success

### run
1. Run: `docker run --rm -it <image>:<tag>`
2. Attach to container stdin/stdout

### up
1. Check if docker-compose.yml exists
2. Run: `docker-compose up --build -d`
3. Show running containers

### stop
1. Run: `docker-compose down` (if compose file exists)
2. Or: `docker stop $(docker ps -q)`
3. Show stopped containers

### push
1. Require image tag as argument
2. Run: `docker push <image>:<tag>`
3. Show push progress

### clean
1. Run: `docker image prune -f`
2. Run: `docker container prune -f`
3. Show freed space

---

If no Docker files exist (no Dockerfile, no docker-compose.yml), create a minimal Dockerfile for the Go project:

```dockerfile
# Build stage
FROM golang:1.24-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -o /app/main .

# Runtime stage
FROM alpine:3.18
RUN apk --no-cache add ca-certificates
WORKDIR /app
COPY --from=builder /app/main .
EXPOSE 8080
CMD ["./main"]
```

Then proceed with the requested action.