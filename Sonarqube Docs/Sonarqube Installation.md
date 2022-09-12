# SonarQube Installation

This guide will help you to set up and configure SonarQube 8.9 LTS versions on Linux servers RedHat version 8. Follow the steps given below for the complete SonarQube configuration.

## SonarQube Requirements
1. A small-scale (individual or small team) instance of the SonarQube server requires at least 2GB of RAM to run efficiently and 1GB of free RAM for the OS.
2. OpenJDK 11 or JRE 11.
3. PostgreSQL version 13 or greater.
4. All sonarquber process should run as a non-root sonar user.

## Install Java Library

```bash
 sudo dnf install java-11-openjdk java-11-openjdk-devel
```

## Install PostgreSQL Database For SonarQube
We will use version 13 of PostgreSQL. If you have no repository yet, follow this steps given below to install PostgreSQL.

```bash
#Install the repository RPM:
 sudo dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm

#Disable the built-in PostgreSQL module:
 sudo dnf -qy module disable postgresql

#Install PostgreSQL:
 sudo dnf install -y postgresql13-server

#Optionally initialize the database and enable automatic start:
 sudo /usr/pgsql-13/bin/postgresql-13-setup initdb
 sudo systemctl enable postgresql-13
 sudo systemctl start postgresql-13
```

If you already have repository, just follow steps given below to install PostgreSQL 13 versions.

```bash
 sudo dnf -y install postgresql13 postgresql13-server postgresql13-contrib
```

Open /var/lib/pgsql/data/pg_hba.conf file to change the authentication to md5.

```bash
 sudo vi /var/lib/pgsql/13/data/pg_hba.conf
```

Find the following lines at the bottom of the file and change peer to trust and idnet to md5

```bash
 ...
 # TYPE  DATABASE        USER            ADDRESS                 METHOD

 # "local" is for Unix domain socket connections only
 local   all             all                                     peer
 # IPv4 local connections:
 host    all             all             127.0.0.1/32            scram-sha-256
 # IPv6 local connections:
 host    all             all             ::1/128                 scram-sha-256
 ...
```

Once changed, it should look like the following.

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

Login to the PostgreSQL CLI with postgres user.

```bash
 psql -U postgres
```

Create a database for SonarQube.

```bash
 CREATE USER sonar WITH PASSWORD 'redhat123';
 CREATE DATABASE sonarqube;
 ALTER DATABASE sonarqube OWNER TO sonar;
 grant all privileges on database sonarqube to sonar ;
 exit
```

## Setup Sonarqube Server
Download sonarqube installation file to /opt folder.

```bash
 cd /opt/
 sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.9.56886.zip
```

Unzip SonarQube source files and rename the folder.

```bash
 sudo unzip sonarqube-8.9.9.56886.zip
 sudo mv sonarqube-8.9.9.56886.zip sonarqube
```

Open /opt/sonarqube/conf/sonar.properties file.

```bash
sudo vi /opt/sonarqube/conf/sonar.properties
```

Uncomment and edit the parameters as shown below. Change the password accordingly. You will find jdbc parameter under PostgreSQL section.

```bash
 ...
 sonar.jdbc.username=sonar
 sonar.jdbc.password=redhat123
 sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube?currentSchema=sonarqubeschema
 sonar.web.host=0.0.0.0
 sonar.web.port=9000
 ...
```

## Add Sonar User and Privileges

Create a user named sonarqube and make it the owner of the /opt/sonarqube directory.

```bash
 sudo useradd sonar
 sudo passwd sonar
 sudo chown -R sonar:sonar /opt/sonarqube
```

Login to the sonarqube PostgreSQL CLI with sonar user.

```bash
 psql sonarqube -U sonar
```

Create a database sonarqube for sonar user.

```bash
 grant all privileges on database sonarqube to sonar;
 CREATE SCHEMA IF NOT EXISTS sonarqubeschema AUTHORIZATION sonar;
 GRANT ALL PRIVILEGES ON DATABASE sonarqube TO sonar;
 GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA sonarqubeschema TO sonar;
 GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA sonarqubeschema TO sonar;
 ALTER USER sonar SET search_path to sonarqubeschema;
 exit
```

