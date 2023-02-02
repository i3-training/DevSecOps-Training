# SonarQube Installation

![sq-projects](../../images/sq.png)

## SonarQube Server Installation

This guide will help you to set up and configure SonarQube 8.9 LTS versions on Ubuntu Server. Follow the steps given below for the complete SonarQube configuration.

## SonarQube Requirements

1. A small-scale (individual or small team) instance of the SonarQube server requires at least 2GB of RAM to run efficiently and 1GB of free RAM for the OS.
2. OpenJDK 11 or JRE 11 above.
3. PostgreSQL version 13 or greater.
4. Add requirements Elasticsearch for resources Linux.
5. All SonarQube process should run as a non-root sonar user.

## Installation Process

### 1. Install Java

You must install Java (Oracle JRE or OpenJDK) on the machine where you plan to run SonarQube:

```bash
sudo apt install openjdk-17-jre-headless
```

### 2. Install PostgreSQL Database For SonarQube

PostgreSQL is required to store SonarQube data you can use other database that mention [Here.](https://docs.sonarqube.org/8.9/requirements/prerequisites-and-overview/#:~:text=8-,Database,-PostgreSQL)
Here we use PostgreSQL, steps below how to install and setup PostgreSQL for SonarQube.

```bash
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
```

after installation is complete start PostgreSQL

```bash
sudo systemctl start postgresql.service
```

Login to postgres user to access the PostgreSQL CLI and create database for PostgreSQL.

```bash
sudo -i -u postgres
psql
```

run this command to create user and database for SonarQube.

```sql
CREATE USER sonar WITH PASSWORD 'redhat123';
CREATE DATABASE sonarqube;
ALTER DATABASE sonarqube OWNER TO sonar;
grant all privileges on database sonarqube to sonar ;
```

Login to the sonarqube PostgreSQL CLI with sonar user:

```bash
 psql sonarqube -U sonar
```

Create a database sonarqube for sonar user:

```bash
grant all privileges on database sonarqube to sonar;
CREATE SCHEMA IF NOT EXISTS sonarqubeschema AUTHORIZATION sonar;
GRANT ALL PRIVILEGES ON DATABASE sonarqube TO sonar;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA sonarqubeschema TO sonar;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA sonarqubeschema TO sonar;
ALTER USER sonar SET search_path to sonarqubeschema;
exit
```

Open `/etc/postgresql/12/main/pg_hba.conf` file to change the authentication to md5, find section containing `"local"` and change its `METHOD` to MD5, exaample below shows final file.

```bash
...
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     md5
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
...
```

You need to restart PostgreSQL service after changed method of `pg_hba.conf`:

```bash
sudo systemctl restart postgresql.service
```

### 3. Setup Sonarqube Server

Download sonarqube installation file to your designated folder you can find the download link [here.](https://www.sonarsource.com/products/sonarqube/downloads/)

```bash
cd /opt/
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.10.61524.zip
```

Unzip SonarQube source files and rename the folder:

```bash
sudo unzip sonarqube-8.9.10.61524.zip
sudo mv sonarqube-8.9.10.61524 sonarqube

# Remove sonarqube zip archive
sudo rm sonarqube-8.9.10.61524.zip
```

Modify configuration for sonarqube located on `/opt/sonarqube/conf/sonar.properties`

1. Modify database connection to make sonarqube connect to database we setup earlier. on this we required to fill username, password and url.

```bash
...
sonar.jdbc.username=sonar
sonar.jdbc.password=redhat123
sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube?currentSchema=sonarqubeschema
...
```

2. Second thing we need to configure is web hosts and port so we can access the web console from web.

```bash
...
sonar.web.host=0.0.0.0
...

...
sonar.web.port=9000
...
```

Create a user named sonar and make it the owner of the `/opt/sonarqube` directory:

```bash
sudo useradd sonar
sudo passwd sonar
sudo chown -R sonar:sonar /opt/sonarqube
```

Setting up SonarQube as a Service:

```bash
sudo vim /etc/systemd/system/sonarqube.service
```

Copy the following content on to the file:

```config
[Unit]
Description=Sonarqube Service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

LimitNOFILE=131072
LimitNPROC=8192

User=sonar
Group=sonar
Restart=always

[Install]
WantedBy=multi-user.target
```

Ensure that:

    - vm.max_map_count is greater than or equal to 524288
    - fs.file-max is greater than or equal to 131072
    - the user running SonarQube can open at least 131072 file descriptors
    - the user running SonarQube can open at least 8192 threads

Set them for the current session by running the following commands as root:

```bash
sudo sysctl -w vm.max_map_count=524288
sudo sysctl -w fs.file-max=131072
ulimit -n 131072
ulimit -u 8192
```

To make it premanently:

```bash
sudo echo "vm.max_map_count=524288" >> /etc/sysctl.conf
sudo echo "fs.file-max=131072" >> /etc/sysctl.conf
```

For the limits:

```bash
sudo vim /etc/security/limits.conf 
```

add following line to the config

```bash
sonarqube       -       nofile          131072
sonarqube       -       nproc           8192
```

Start SonarQube with following this commands:

```bash
sudo systemctl enable --now sonarqube 
sudo systemctl status sonarqube
```

## SonarQube Scanner Installation



## SonarQube jenkins Integration
