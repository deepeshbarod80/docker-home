
1. ''' Docker Setup '''
- 1) Install & Configure Docker
# sudo apt-get update
# sudo apt-get install docker.io -y

- Grant Permission and making Docker group owner
# sudo usermod -aG docker ubuntu && newgrp docker
- It will refresh the group config hence no need to restart the EC2 machine.

- 2) Docker Compose Installation
- Download the Docker Compose binary:
# sudo curl -L "https://github.com/docker/compose/releases/download/v2.23.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

- Download the Docker Compose GPG key:
# sudo curl -L "https://github.com/docker/compose/releases/download/v2.23.0/docker-compose-$(uname -s)-$(uname -m).sha256" -o /tmp/docker-compose.sha256

- Verify the downloaded binary:
# echo "$(cat /tmp/docker-compose.sha256)  /usr/local/bin/docker-compose" | sha256sum --check

- Make the binary executable:
# chmod +x /usr/local/bin/docker-compose

- Verify the installation:
# docker-compose version

- 3) (Optional) Enable Command Completion - Docker compose
- Download the completion script:
# sudo curl -L https://raw.githubusercontent.com/docker/compose/v2.23.0/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose

- Reload your shell:
# source ~/.bashrc

- 4) (Optional) Install Docker Compose as a Docker Plugin
- Create the Docker CLI plugins directory:
# mkdir -p ~/.docker/cli-plugins

- Download Docker Compose as a plugin:
# curl -L "https://github.com/docker/compose/releases/download/v2.23.0/docker-compose-$(uname -s)-$(uname -m)" -o ~/.docker/cli-plugins/docker-compose

- Make the plugin executable:
# chmod +x ~/.docker/cli-plugins/docker-compose

- Verify the installation:
# docker-compose version
