
# Dockerfile: Complete Guide with Industry Standards

Here's a comprehensive guide to writing a Dockerfile following industry best practices, with explanations for each component.

## Basic Dockerfile Structure

```dockerfile
# Specify the base image
FROM [image]:[tag]

# Set maintainer (deprecated in favor of LABEL)
# MAINTAINER Your Name <your.email@example.com>

# Set metadata
LABEL maintainer="Your Name <your.email@example.com>"
LABEL version="1.0"
LABEL description="A sample Dockerfile for demonstration purposes"

# Set environment variables
ENV NODE_ENV=production
ENV APP_PORT=3000

# Set working directory
WORKDIR /app

# Copy files (prefer COPY over ADD for most cases)
COPY package*.json ./
COPY . .

# Run commands during build
RUN npm install --production

# Expose ports
EXPOSE 3000

# Define entry point
ENTRYPOINT ["npm"]

# Set default command
CMD ["start"]
```

---

## Detailed Explanation of Each Instruction

### 1. `FROM`
**Purpose**: Specifies the base image
**Best Practices**:
- Always use specific tags (avoid `latest`)
- Choose minimal images (e.g., `alpine` variants)
- Consider multi-stage builds for production

```dockerfile
FROM node:18-alpine  # Good - specific version and lightweight
# Avoid: FROM node (uses latest and full image)
```

### 2. `LABEL`
**Purpose**: Adds metadata to your image
**Replaces**: The deprecated `MAINTAINER` instruction
**Best Practices**:
- Use reverse DNS notation for custom labels
- Group related labels

```dockerfile
LABEL org.opencontainers.image.authors="team@example.com"
LABEL org.opencontainers.image.version="1.0.0"
```

### 3. `ENV`
**Purpose**: Sets environment variables
**Best Practices**:
- Use for configuration that might need to change
- Group related variables
- Consider defaults that can be overridden at runtime

```dockerfile
ENV APP_HOME=/app \
    NODE_ENV=production
```

### 4. `WORKDIR`
**Purpose**: Sets the working directory
**Best Practices**:
- Always set absolute paths
- Set early to ensure consistency in subsequent commands
- Prevents operations in unintended directories

```dockerfile
WORKDIR /app
```

### 5. `COPY` vs `ADD`
**COPY**: Simply copies files from host to container
**ADD**: Has additional features (URL downloads, tar extraction)
**Best Practices**:
- Prefer `COPY` unless you need `ADD`'s special features
- Be specific about what you copy
- Use `.dockerignore` to exclude unnecessary files

```dockerfile
COPY package*.json ./  # Copy only package files first
COPY . .              # Then copy everything else
```

### 6. `RUN`
**Purpose**: Executes commands during build
**Best Practices**:
- Combine related commands with `&&` and `\` for readability
- Clean up after installations to reduce image size
- Avoid unnecessary packages

```dockerfile
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
      git \
      curl && \
    rm -rf /var/lib/apt/lists/*
```

### 7. `EXPOSE`
**Purpose**: Documents which ports the container listens on
**Note**: Doesn't actually publish ports (done at runtime with `-p`)
**Best Practices**:
- Document all ports your app uses
- Match the actual application configuration

```dockerfile
EXPOSE 3000  # For web applications
EXPOSE 5432  # For databases
```

### 8. `ENTRYPOINT` and `CMD`
**ENTRYPOINT**: The executable that will run
**CMD**: Default arguments to the entrypoint
**Best Practices**:
- Use exec form (`["executable", "param"]`) not shell form
- Consider using a wrapper script for complex startup
- Use `CMD` for default arguments that can be easily overridden

```dockerfile
ENTRYPOINT ["/usr/bin/docker-entrypoint.sh"]
CMD ["start"]
```

---


## Advanced Features

### Multi-stage Builds
Reduces final image size by discarding build dependencies

```dockerfile
# Build stage
FROM node:18 as builder
WORKDIR /build
COPY . .
RUN npm install && npm run build

# Production stage
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /build/dist ./dist
COPY package*.json ./
RUN npm install --production
EXPOSE 3000
CMD ["node", "dist/main.js"]
```

### Healthcheck
Adds container health validation

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:3000/health || exit 1
```

### User Context
Improves security by not running as root

```dockerfile
RUN groupadd -r appuser && useradd -r -g appuser appuser
USER appuser
```

## Complete Industry-Standard Example

```dockerfile
# Build stage
FROM node:18-alpine as builder

LABEL org.opencontainers.image.authors="devops@example.com"
LABEL org.opencontainers.image.vendor="Example Inc."

WORKDIR /build

# Copy dependency definitions first for better caching
COPY package*.json ./
COPY tsconfig*.json ./

# Install dependencies
RUN npm ci

# Copy source files
COPY src/ ./src/

# Build application
RUN npm run build

# Production stage
FROM node:18-alpine

ENV NODE_ENV=production \
    APP_PORT=3000

WORKDIR /app

# Create non-root user
RUN addgroup -S appgroup && \
    adduser -S appuser -G appgroup && \
    chown appuser:appgroup /app

# Copy from builder
COPY --from=builder --chown=appuser:appgroup /build/dist/ ./dist/
COPY --from=builder --chown=appuser:appgroup /build/package*.json ./

# Install production dependencies only
RUN npm ci --only=production

USER appuser

EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:3000/health || exit 1

ENTRYPOINT ["node"]
CMD ["dist/main.js"]
```

## Key Industry Standards to Follow

1. **Minimal Base Images**: Use `alpine` or `slim` variants
2. **Multi-stage Builds**: Separate build and runtime environments
3. **Non-root User**: Improve security by not running as root
4. **Proper Layer Caching**: Order commands to maximize cache usage
5. **`.dockerignore`**: Create this file to exclude unnecessary files
6. **Linting**: Use `hadolint` to check your Dockerfile for issues
7. **Image Scanning**: Scan built images for vulnerabilities
8. **Deterministic Builds**: Pin versions for all dependencies

---

