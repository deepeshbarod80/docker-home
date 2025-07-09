
"These Commands are only to install tools and dependencies inside the container"

Alpine Container: <terra-local-gateway>
Ubuntu Container: <terra-local-app>

1. SSH Connection Setup
# For Alpine container
- docker exec terra-local-wanderlust apk add --no-cache openssh
- docker exec terra-local-wanderlust mkdir -p /root/.ssh
- docker cp terra-key.pub terra-local-wanderlust:/root/.ssh/authorized_keys
- docker exec terra-local-wanderlust chmod 600 /root/.ssh/authorized_keys
- docker exec terra-local-wanderlust /usr/sbin/sshd -D &

# For Ubuntu container
- docker exec terra-local-wanderlust-app apt update
- docker exec terra-local-wanderlust-app apt install -y openssh-server
- docker exec terra-local-wanderlust-app mkdir -p /run/sshd
- docker cp terra-key.pub terra-local-wanderlust-app:/root/.ssh/authorized_keys
- docker exec terra-local-wanderlust-app service ssh start


# Test Connection - Check SSH is running
- docker exec terra-local-wanderlust ps aux | grep sshd
- docker exec terra-local-wanderlust-app service ssh status

- ssh -v root@127.0.0.1 -p 32222 -i terra-key.pub
-----------------------------------------------------------------------


2. Alpine Container installations
# Install Java-17 + Docker:
- docker exec -it terra-local-gateway /bin/sh
- apk update
- apk add --no-cache openjdk17 docker-cli

# Install Jenkins
- wget -O /opt/jenkins.war https://get.jenkins.io/war-stable/latest/jenkins.war
# Get initial admin password
docker exec <terra-local-gateway> cat /root/.jenkins/secrets/initialAdminPassword

# Install SonarQube
- wget -O /opt/sonarqube.zip https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.4.1.88267.zip
- unzip /opt/sonarqube.zip -d /opt/
# Default credentials
Username: admin
Password: admin

# Install Trivy (All-in-one install)
docker exec -it --user root terra-local-gateway /bin/sh -c "
apk add --no-cache wget tar && \
wget https://github.com/aquasecurity/trivy/releases/download/v0.60.0/trivy_0.60.0_Linux-64bit.tar.gz && \
tar -xzf trivy_*.tar.gz && \
chmod +x trivy && \
mv trivy /usr/local/bin/ && \
rm trivy_*.tar.gz && \
trivy --version"

# Install OWASP ZAP
apk add --no-cache zaproxy
--------------------------------------------------------------------------------


3. Ubuntu Container installations
# System updates
- apt-get update && apt-get upgrade -y

# Install Java 17
- apt-get install -y openjdk-17-jdk

# Install Jenkins (Latest LTS)
- curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | gpg --dearmor -o /usr/share/keyrings/jenkins.gpg
- echo "deb [signed-by=/usr/share/keyrings/jenkins.gpg] https://pkg.jenkins.io/debian-stable binary/" | tee /etc/apt/sources.list.d/jenkins.list
- apt-get update
- apt-get install -y jenkins

# Install Docker
- curl -fsSL https://get.docker.com | sh
- usermod -aG docker jenkins

# Install Docker Compose V2
- mkdir -p ~/.docker/cli-plugins
- curl -SL https://github.com/docker/compose/releases/latest/download/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose
- chmod +x ~/.docker/cli-plugins/docker-compose

# Install Trivy
- docker exec -it --user root terra-local-app /bin/bash
# Install from official repo
- apt-get update && apt-get install -y wget apt-transport-https gnupg
- wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | apt-key add -
- echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/trivy.list
- apt-get update
- apt-get install -y trivy
- trivy --version    # Verify installation

# Install SonarQube (Community Edition)
- wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.4.1.88267.zip
- unzip sonarqube-*.zip -d /opt/
- mv /opt/sonarqube-* /opt/sonarqube

# Install OWASP ZAP
- apt-get install -y zaproxy

# Start services
- systemctl start jenkins
- /opt/sonarqube/bin/linux-x86-64/sonar.sh start
- zap.sh -daemon -port 8090 -host 0.0.0.0 &'


