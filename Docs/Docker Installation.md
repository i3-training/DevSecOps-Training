#Docker Installation

##Install Docker Engine on RHEL
To get started with Docker Engine on RHEL, make sure you meet the prerequisites, then install Docker.

##Uninstall old versions and podman
it is better to delete podman first to avoid conflicts between podman and docker
sudo yum remove docker \
 docker-client \
 docker-client-latest \
 docker-common \
 docker-latest \
 docker-latest-logrotate \
 docker-logrotate \
 docker-engine \
 podman \
 runc >.
