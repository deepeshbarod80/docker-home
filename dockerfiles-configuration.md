1. Dockerfile Components & Syntax

"Component"	 "Syntax Example"	     "Purpose"

[FROM]	     FROM node:18-alpine	 Base image (OS + preinstalled software)

[WORKDIR]	 WORKDIR /app	         Sets working directory inside container

[COPY]	     COPY package*.json ./   Copies files from host to container

[RUN]	     RUN npm install	     Executes commands  during image build

[EXPOSE]	 EXPOSE 3000	         Documents port (doesn't publish it)

[ENV]	     ENV NODE_ENV=production  Sets environment variables

[CMD]	     CMD ["npm", "start"]	 Default command to run when container starts

[ENTRYPOINT] ENTRYPOINT ["python3"]  Configures container to run as executable

[ARG]	     ARG APP_VERSION=1.0	 Build-time variables (not persisted in image)

[VOLUME]	 VOLUME ["/data"]	     Creates mount point for persistent storage

[USER]	     USER node	             Specifies non-root user for security

[HEALTHCHECK] HEALTHCHECK --interval=30s...	  Container health monitoring
---------------------------------------------------------------------------------------


2. 1-Tier App (Monolithic)
Example: 
- Node.js app with frontend + backend in single container
# Basic COPY/RUN/CMD commands

// Dockerfile:
--------------------------------------------------->
```yaml
# Base image (OS + preinstalled software)
FROM node:18-alpine

# Sets working directory inside container
WORKDIR /app

# Copy dependency files first (caching optimization)
COPY package*.json ./
RUN npm ci

# Copy source code
COPY . .

# Build and run
RUN npm run build

# Expose port 3000
EXPOSE 3000

# Default command to run when container starts
CMD ["npm", "start"]
```
--------------------------------------------------->

// App-Repo Structure:
```yml
my-app/
├── Dockerfile
├── package.json
├── src/
│   ├── frontend/
│   └── backend/
```
----------------------------------------------------------------------------------------


3. 2-Tier App (Client-Server)
Example: 
- React Frontend + Spring Boot Backend
# Docker Multi-stage builds, network isolation

// Frontend Dockerfile:
---------------------------->
```yaml
        |<--------------------- # Stage 1: Build ------------------>|
# Base image (OS + preinstalled software)
FROM node:18-alpine AS builder

# Sets working directory inside container
WORKDIR /app

# Copy dependency files first (caching optimization)
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy source code
COPY . .

# Build and run
RUN npm run build

        |<--------------------- # Stage 2: Runtime ------------------>|
# pulling nginx image from docker hub
FROM nginx:alpine

# Copy the code from the container to the nginx container
COPY --from=builder /app/build /usr/share/nginx/html

# Expose port 3000
EXPOSE 3000

# Default command to run when container starts
CMD ["nginx", "-g", "daemon off;"]
```
--------------------------->


// Backend Dockerfile:
```yaml
        |<--------------------- # Stage 1: Build ------------------>|
# Base image (OS + preinstalled software)
FROM maven:3.8.6-openjdk-17 AS build

# Sets working directory inside container
WORKDIR /app

# Copy dependency files first (caching optimization)
COPY pom.xml .

# Build and run the project dependencies
RUN mvn dependency:go-offline

# Copy source code
COPY src ./src

# Build and run the project packages 
RUN mvn package -DskipTests

        |<--------------------- # Stage 2: Runtime ------------------>|
# pulling openjdk image from docker hub
FROM openjdk:17-jdk-slim

# Copy the code from the container to the openjdk container
COPY --from=build /app/target/*.jar app.jar

# Expose port 8080
EXPOSE 8080

# Default command to run when container starts
ENTRYPOINT ["java","-jar","app.jar"]
```
--------------------------->

// App-Repo Structure:
```yaml
my-app/
├── package.json
├── src/
│   ├── frontend/Dockerfile
│   └── backend/Dockerfile
```
-----------------------------------------------------------------------------------



4. 3-Tier App (Presentation + Business Logic + Data)
Example: 
- Next.js Frontend + Django API + MySQL  

# Docker Volumes, environment variables, depends_on

// Frontend Dockerfile:
-------------------->
```yaml
        |<--------------------- # Stage 1: Build ------------------>|
# Base image (OS + preinstalled software)
FROM node:18-alpine AS builder

# Sets working directory inside container
WORKDIR /app

# Copy dependency files first (caching optimization)
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy source code
COPY . .

# Build and run
RUN npm run build

        |<--------------------- # Stage 2: Runtime ------------------>|
# pulling nginx image from docker hub
FROM nginx:alpine

# Copy the code from the container to the nginx container
COPY --from=builder /app/out /usr/share/nginx/html

# Copy the nginx config file to the default nginx config directory
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Expose port 3000
EXPOSE 3000
```
-------------------->

// Backend Dockerfile:
```yaml
# Base image of python  
FROM python:3.11-slim

# Sets working directory inside container
WORKDIR /app

# Copy dependency files from app-repo from requirements.txt to container
COPY requirements.txt .

# Install dependencies from requirements.txt file
RUN pip install --no-cache-dir -r requirements.txt

# Copy source code
COPY . .

# Expose port 8000
EXPOSE 8000

# Default command to run when container starts
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "myproject.wsgi"]
```

----------------------------------------------------------------------------------



