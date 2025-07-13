
# Multi-stage Dockerfile for Django (Python) + React + MySQL Stack

Here's a comprehensive Dockerfile and docker-compose setup for a Python Django backend, React frontend, and MySQL database:

## Dockerfile (Multi-stage)

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
FROM python:3.11-slim as backend-builder

WORKDIR /backend

# Install system dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    gcc \
    python3-dev \
    default-libmysqlclient-dev \
    && rm -rf /var/lib/apt/lists/*

# Create and activate virtual environment
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# Install Python dependencies
COPY backend/requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy backend source code
COPY backend/ .

# Collect static files (if applicable)
RUN python manage.py collectstatic --noinput

# ================================================
#                PRODUCTION STAGE
# ================================================
FROM python:3.11-slim

# Create non-root user
RUN addgroup --system django && \
    adduser --system --ingroup django django

WORKDIR /app

# Copy virtual environment from builder
COPY --from=backend-builder /opt/venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# Copy backend code
COPY --from=backend-builder --chown=django:django /backend /app

# Copy built frontend
COPY --from=frontend-builder --chown=django:django /frontend/build /app/frontend/build

# Environment variables
ENV PYTHONUNBUFFERED=1 \
    DJANGO_SETTINGS_MODULE=core.settings.production \
    DATABASE_URL=mysql://django:password@mysql:3306/appdb \
    SECRET_KEY=your-secret-key-here \
    DEBUG=0

# Expose port
EXPOSE 8000

# Health check
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:8000/health/ || exit 1

USER django

# Entrypoint script
COPY docker/django/entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

# Default command
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "core.wsgi"]
```

---


## Entrypoint Script (`docker/django/entrypoint.sh`)

```bash
#!/bin/sh

# Wait for MySQL to be ready
echo "Waiting for MySQL..."
while ! nc -z mysql 3306; do
  sleep 1
done
echo "MySQL started"

# Run migrations
python manage.py migrate --noinput

# Create superuser (optional, for development)
if [ "$CREATE_SUPERUSER" = "1" ]; then
  python manage.py createsuperuser --noinput
fi

exec "$@"
```

## docker-compose.yml

```yaml
version: '3.8'

services:
  # Django backend service
  backend:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8000:8000"
    environment:
      - DJANGO_SETTINGS_MODULE=core.settings.production
      - DATABASE_URL=mysql://django:password@mysql:3306/appdb
      - SECRET_KEY=your-secret-key-here
      - DEBUG=0
      - CREATE_SUPERUSER=1  # Only for development
    depends_on:
      - mysql
    volumes:
      - ./backend:/app  # For development, mount code
    restart: unless-stopped

  # React frontend service (development)
  frontend:
    image: node:18-alpine
    working_dir: /frontend
    ports:
      - "3000:3000"
    volumes:
      - ./frontend:/frontend
      - /frontend/node_modules
    command: ["npm", "start"]
    depends_on:
      - backend
    restart: unless-stopped

  # MySQL database service
  mysql:
    image: mysql:8.0
    ports:
      - "3306:3306"
    environment:
      MYSQL_DATABASE: appdb
      MYSQL_USER: django
      MYSQL_PASSWORD: password
      MYSQL_ROOT_PASSWORD: rootpassword
    volumes:
      - mysql-data:/var/lib/mysql
      - ./docker/mysql/init.sql:/docker-entrypoint-initdb.d/init.sql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      timeout: 20s
      retries: 10
    restart: unless-stopped

  # Redis (optional for caching)
  redis:
    image: redis:alpine
    ports:
      - "6379:6379"
    restart: unless-stopped

  # Nginx (optional for production)
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./docker/nginx/nginx.conf:/etc/nginx/conf.d/default.conf
      - ./frontend/build:/usr/share/nginx/html
    depends_on:
      - backend
    restart: unless-stopped

volumes:
  mysql-data:
```

---


## Project Structure (for Python Django, React, and MySQL)

```bash
project-root/
  ├── backend/
  │   ├── core/
  │   │   ├── settings/
  │   │   │   ├── base.py
  │   │   │   ├── production.py
  │   │   │   └── development.py
  │   ├── manage.py
  │   └── requirements.txt
  ├── frontend/
  │   ├── public/
  │   ├── src/
  │   ├── package.json
  │   └── ... (React files)
  ├── docker/
  │   ├── django/
  │   │   └── entrypoint.sh
  │   ├── mysql/
  │   │   └── init.sql
  │   └── nginx/
  │       └── nginx.conf
  ├── Dockerfile
  └── docker-compose.yml
```

---


## Key Features of This Setup:

1. **Multi-stage Build**:
   - Separate stages for frontend (React) and backend (Django)
   - Final production image is slim and secure

2. **Database Integration**:
   - MySQL with proper initialization
   - Health checks and connection waiting
   - Persistent data volume

3. **Development vs Production**:
   - Frontend served separately in development (with hot-reload)
   - Django code mounted in development
   - Different settings modules for environments

4. **Security**:
   - Non-root user execution
   - Environment variables for sensitive data
   - Virtual environment isolation

5. **Scalability**:
   - Ready for Redis caching
   - Nginx proxy option
   - Health checks for all services


---


## Deployment Instructions

1. For development:
   ```bash
   docker-compose up -d --build
   ```
   - Frontend: http://localhost:3000 (with hot-reload)
   - Backend: http://localhost:8000

2. For production:
   ```bash
   docker-compose -f docker-compose.yml -f docker-compose.prod.yml up -d --build
   ```
   (You would create a separate production compose file with appropriate settings)

3. To stop:
   ```bash
   docker-compose down
   ```

---