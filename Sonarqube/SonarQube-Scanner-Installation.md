# SonarQube Scanner Installation

## Install Git



```bash
 # If you’re on Fedora (or RHEL and CentOS), you can use dnf:
 sudo dnf install git-all
```

```bash
 # If you’re on a Debian-based distribution, such as Ubuntu, try apt:
 sudo apt install git-all
```

To check git use git command

```bash
 git

 # Check git version
 git --version
```

Set your user name and email address.

``bash
 git config --global user.name "Your NameHere"
 git config --global user.email Your@email.com
```

```bash
 mkdir sonar-scanner
```

Install sonar-scanner in sonar-scanner directory with this command.

```bash
 wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.7.0.2747-linux.zip
```

Unzip rpm with this command.

```bash
 unzip sonar-scanner-cli-4.7.0.2747-linux.zip
```