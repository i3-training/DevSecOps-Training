# Create Docker Internal image registry

## Prerequisite

- [ ] Java 8 Runtime Environment
- [ ] Minimum 1 VCPU & 2 GB Memory
- [ ] OpenJDK 8
- [ ] All Nexus processes should run as a non-root nexus user.

## Installation

### 1. install Java OpenJDK 8

install java open jdk 8 on your server

```bash
sudo yum install java-1.8.0-openjdk-devel
```

### 2. Installing Nexus

create new directory for nexus installation in root directory `/app`

```bash
sudo mkdir /app && cd /app
```

Download latest nexus using following [link](https://help.sonatype.com/repomanager3/download), then extract the archive to nexus directory

```bash
sudo wget -O nexus.tar.gz https://download.sonatype.com/nexus/3/nexus-3.41.1-01-unix.tar.gz
sudo tar -xvf nexus.tar.gz
sudo mv nexus-3*** nexus
```

Add new user to run nexus and give the new user ownership to the nexus binary

```bash
sudo adduser nexus
sudo chown -R nexus:nexus /app/nexus
sudo chown -R nexus:nexus /app/sonatype-work
```

modify file `/app/nexus/bin/nexus.rc` to make nexus run as a user we created before

```conf
run_as_user="nexus"
```

add a SELinux policy to allow Systemd to access the nexus binary in path `/app/nexus/bin/nexus` using the following command.

```bash
sudo chcon -R -t bin_t /app/nexus/bin/nexus
```

Nexus require java jdk 8 to run if you have more than one java version installed you need to define where java that nexus will be using by modify `/app/nexus/bin/nexus` file.

```bash
sudo vim /app/nexus/bin/nexus
```

locate the line INSTALL4J_JAVA_HOME_OVERRIDE. Remove the hash and specify the location of your JDK/JRE:

```conf
INSTALL4J_JAVA_HOME_OVERRIDE=/usr/lib/jvm/java-1.8.0-openjdk
```

### 3. Configure nexus as System Service

It is better to have systemd entry to manage nexus using systemctl, for adding nexus as a systemd service. First user need to create a nexus systemd unit file.

```bash
sudo vim /etc/systemd/system/nexus.service
```

add content below to `/etc/systemd/system/nexus.service`

```conf
[Unit]
Description=nexus service
After=network.target

[Service]
Type=forking
LimitNOFILE=65536
User=nexus
Group=nexus
ExecStart=/app/nexus/bin/nexus start
ExecStop=/app/nexus/bin/nexus stop
User=nexus
Restart=on-abort

[Install]
WantedBy=multi-user.target
```

add nexus service to boot.

```bash
sudo chkconfig nexus on
```

To start the Nexus service, use the following command.

```bash
sudo systemctl start nexus
```

### 3. Login to nexus web interface

