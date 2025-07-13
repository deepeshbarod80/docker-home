
---

# Multi-stage Dockerfile for a MERN Stack Application

Here's a comprehensive Dockerfile for a 3-tier MERN (MongoDB, Express, React, Node.js) application with separate stages for frontend, backend, and database setup following industry best practices:

```dockerfile
# ================================================
#                BUILD STAGE - FRONTEND
# ================================================
FROM node:18-alpine as frontend-builder

WORKDIR /frontend

# Copy package files first for better caching
COPY frontend/package*.json ./

# Install frontend dependencies
RUN npm ci

# Copy remaining frontend files
COPY frontend/ .

# Build React application
RUN npm run build

# ================================================
#                BUILD STAGE - BACKEND
# ================================================
FROM node:18-alpine as backend-builder

WORKDIR /backend

# Copy package files first for better caching
COPY backend/package*.json ./

# Install backend dependencies
RUN npm ci --only=production

# Copy remaining backend files
COPY backend/ .

# ================================================
#                PRODUCTION STAGE
# ================================================
FROM node:18-alpine

# Create non-root user for security
RUN addgroup -S appgroup && \
    adduser -S appuser -G appgroup

# Set environment variables
ENV NODE_ENV=production \
    PORT=5000 \
    MONGODB_URI=mongodb://mongo:27017/mernapp \
    JWT_SECRET=your_jwt_secret \
    CLIENT_URL=http://localhost:3000

WORKDIR /app

# Copy built frontend from frontend-builder
COPY --from=frontend-builder --chown=appuser:appgroup /frontend/build ./frontend/build

# Copy backend from backend-builder
COPY --from=backend-builder --chown=appuser:appgroup /backend ./backend

WORKDIR /app/backend

# Expose backend port
EXPOSE 5000

# Health check
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:5000/api/health || exit 1

USER appuser

# Start the application
CMD ["node", "server.js"]

# ================================================
#                DATABASE SETUP (docker-compose)
# Note: MongoDB is typically run as a separate service
# rather than in the same container
# ================================================
```

---


## Companion `docker-compose.yml` File

Since MongoDB should run as a separate container, here's the corresponding docker-compose.yml:

```yaml
version: '3.8'

services:
  # Backend service
  backend:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "5000:5000"
    environment:
      - NODE_ENV=production
      - PORT=5000
      - MONGODB_URI=mongodb://mongo:27017/mernapp
      - JWT_SECRET=your_jwt_secret_here
      - CLIENT_URL=http://localhost:3000
    depends_on:
      - mongo
    restart: unless-stopped

  # Frontend service (optional - could be served from backend)
  frontend:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    command: ["npm", "start"]
    depends_on:
      - backend
    restart: unless-stopped

  # MongoDB service
  mongo:
    image: mongo:6.0
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: example
    restart: unless-stopped

  # Mongo Express (optional web UI)
  mongo-express:
    image: mongo-express
    ports:
      - "8081:8081"
    environment:
      ME_CONFIG_MONGODB_SERVER: mongo
      ME_CONFIG_MONGODB_ADMINUSERNAME: root
      ME_CONFIG_MONGODB_ADMINPASSWORD: example
    depends_on:
      - mongo
    restart: unless-stopped

volumes:
  mongo-data:
```

---


## Key Features of This Setup:

1. **Multi-stage Build**:
   - Separate stages for frontend and backend
   - Final image only contains production artifacts

2. **Security Best Practices**:
   - Non-root user execution
   - Minimal base images (alpine)
   - Proper file permissions

3. **Proper Layer Caching**:
   - Package files copied before source code
   - Dependencies installed before copying application code

4. **Database Separation**:
   - MongoDB runs in its own container
   - Proper volume for data persistence

5. **Environment Configuration**:
   - Critical settings configurable via environment variables
   - Sensitive data should use secrets in production

6. **Health Monitoring**:
   - Healthcheck for backend service
   - Automatic restarts on failure

## Project Structure Assumption

- This assumes the following project structure:
```bash
project-root/
  ├── backend/
  │   ├── package.json
  │   ├── server.js
  │   └── ... (other backend files)
  ├── frontend/
  │   ├── package.json
  │   ├── public/
  │   ├── src/
  │   └── ... (React files)
  ├── Dockerfile
  └── docker-compose.yml
```

## Deployment Instructions

1. Build and start the services:
   ```bash
   docker-compose up -d --build
   ```

2. Access the application:
   - Frontend: http://localhost:3000
   - Backend API: http://localhost:5000
   - Mongo Express: http://localhost:8081

3. Stop the services:
   ```bash
   docker-compose down
   ```

---