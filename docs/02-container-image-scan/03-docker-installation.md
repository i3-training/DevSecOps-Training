# Docker Installation

![docker-logo](../../images/dockerlogo-trivy.png)

## Install Docker Engine on RHEL

To get started with Docker Engine on RHEL, make sure you meet the prerequisites, then install Docker.

## OS Requirements

To install Docker Engine, you need a maintained version of Ubuntu 
## Uninstall old versions docker

Older versions of Docker if these are installed, uninstall them, along with associated dependencies.

```bash
sudo apt-get remove docker docker-engine docker.io containerd runc
```

## Installation using the repository

Before you install Docker Engine for the first time on a new host machine, you need to set up the Docker repository. Afterward, you can install and update Docker from the repository.

### Set up the repository

Install required dependency for installation.

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg lsb-release
```

Add Docker’s official GPG key

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

Use the following command to set up the repository.

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### Install the docker engine

1. Install the latest version of Docker Engine, containerd, and Docker Compose or go to the next step to install a specific version:

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

2. To install a specific version of Docker Engine, list the available versions in the repo, then select and install:

```bash
# List and sort the versions available in your repo. This example sorts results by version number, highest to lowest, and is truncated:
apt-cache madison docker-ce | awk '{ print $3 }'

# Install a specific version
VERSION_STRING=5:20.10.13~3-0~ubuntu-jammy
sudo apt-get install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-compose-plugin
```

### Start Docker

Not forget to turn on the docker service.

```bash
#start service
sudo systemctl start docker.service

#Check status service
sudo systemctl status docker.service

```

### Run Docker as non Root

If you don’t want to preface the docker command with sudo, create a Unix group called docker and add users to it. When the Docker daemon starts, it creates a Unix socket accessible by members of the docker group. 

1. Create the docker group.

```bash
sudo groupadd docker
```

if docker group already exists, you can skip this step.

2. Add your user to the docker group.

```bash
sudo usermod -aG docker $USER
```

3. Log out and log back in so that your group membership is re-evaluated. (Restart if necessary).

```bash
newgrp docker
```

### Deploy Container

Verify that Docker Engine is installed correctly by running the hello-world image.

```bash
#This command downloads a test image and runs it in a container. When the container runs, it prints a message and exits.
docker run hello-world
```
