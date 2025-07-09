
# **MySQL Database Container Configuration**

1. Pull & Run MySQL Container
- Use this command to start a MySQL container:

```bash
docker run -d \
  --name mysql \
  --network two-tier \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=devops \
  -e MYSQL_USER=deepesh \
  -e MYSQL_PASSWORD=root \
  -p 3306:3306 \
  -v mysql_data:/var/lib/mysql \
  mysql:latest
```

```bash
docker run -d \
  --name mysql \
  --network two-tier \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=devops \
  mysql
```

```bash
docker run -d \
  --name two-tier-app \
  --network two-tier \
  -p 5000:5000 \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=deepesh \
  -e MYSQL_PASSWORD=root \
  -e MYSQL_DB=devops \
  -v flask-app_data:/app/data \
  two-tier-backend:latest

# or,

docker run -d -p 5000:5000 --network two-tier -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=root -e MYSQL_DATABASEB=devops two-tier-backend:latest


# Parameters explained:

-d: Run in detached mode

--name: Container name

-e MYSQL_ROOT_PASSWORD: Root password (required)

-e MYSQL_DATABASE: Create initial database

-e MYSQL_USER: Create regular user

-e MYSQL_PASSWORD: Password for regular user

-p 3306:3306: Map container port to host port

-v mysql_data:/var/lib/mysql: Persistent storage
```

---


2. Connect to MySQL
* Option 1: Direct MySQL client access
```bash
mysql -h 127.0.0.1 -P 3306 -u root -p
```

* Option 2: Access through container

```bash
# Get into container's bash
docker exec -it mysql-container /bin/bash
# Then connect using MySQL client
mysql -u root -p
```

# Output;
```txt
"Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 9.2.0 MySQL Community Server - GPL

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement."
```

---


3. Verify Connection
After logging in, run SQL commands:
```sql
SHOW DATABASES;
SELECT User FROM mysql.user;
```

// After Login as root in mysql container;
# SHOW DATABASES
Output;
```
LECT User FROM mysql.user;+--------------------+
| Database           |
+--------------------+
| information_schema |
| my-database        |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

```

# SELECT User FROM mysql.user
Output;
```
+------------------+
| User             |
+------------------+
| mysqldb          |
| root             |
| mysql.infoschema |
| mysql.session    |
| mysql.sys        |
| root             |
+------------------+
6 rows in set (0.00 sec)

```

---


4. Using Docker Compose <recommended instead of directly run docker commands>
- Create docker-compose.yaml:           <mysql and phpmyadmin>

```yaml
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    container_name: mysql-db
    environment:
      MYSQL_ROOT_PASSWORD: secure-root-pass
      MYSQL_DATABASE: mysql_db
      MYSQL_USER: mysqldb_user
      MYSQL_PASSWORD: mysqldb_pass
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    links:
      - mysql
    ports:
      - 8081:80
    environment:
      PMA_HOST: mysql

volumes:
  mysql_data:
```

- Then, Start with docker-compose to start the container:
# docker-compose up -d
- It will start the container
---------------------------------------------------------------------------------



5. **Docker Compose Setup** <with-mysql-and-flask-app>
```yaml
version: '3.8'

services:
  mysql:
    image: mysql:latest
    container_name: mysql
    networks:
      - two-tier
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: my-database
      MYSQL_USER: mysqldb
      MYSQL_PASSWORD: Soobi@123S
    volumes:
      - mysql_data:/var/lib/mysql
    command: 
      --default-authentication-plugin=mysql_native_password

  flask-app:
    image: two-tier-backend
    networks:
      - two-tier
    ports:
      - "5000:5000"
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: mysqldb
      MYSQL_PASSWORD: Soobi@123S
      MYSQL_DB: my-database
    depends_on:
      mysql:
        condition: service_healthy

networks:
  two-tier:
    driver: bridge

volumes:
  mysql_data:
```
---------------------------------------------------------------------->




6. Important Considerations
1) Security;
- Always use strong passwords
- Never expose to public networks without protection
- Consider using secrets for production

2) Persistence;
- Without volume, data is lost when container stops
- Volume mysql_data preserves data between restarts

3) Version Tags;
- Specify version tag (e.g., mysql:8.0) instead of latest

4) Configuration Files
- To add custom config:
```yaml
volumes:
  - ./my.cnf:/etc/mysql/conf.d/my.cnf
```

---


7. Troubleshooting
```bash
# Check logs: 
docker logs mysql-container
# Test connection: 
telnet 127.0.0.1 3306
# Verify container status: 
docker ps -a
# Reset credentials: 
# Delete container and volume, then recreate
```

---
