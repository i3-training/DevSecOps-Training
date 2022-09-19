# SonarQube Scanner Installation

## Install Git

Chances are that the git command is already available on your RHEL 8 system.
Execute git --version to check whether the git tool is already installed.

Download git with following this command:

```bash
 # If you’re on Fedora (or RHEL and CentOS), you can use dnf:
 sudo dnf install git-all
```

```bash
 # If you’re on a Debian-based distribution, such as Ubuntu, try apt:
 sudo apt install git-all
```

To check git use git command and confirm installation by checking the git command version number:

```bash
 git

 # Check git version
 git --version
```

Set your user name and email address:

```bash
 git config --global user.name "Your NameHere"
 git config --global user.email Your@email.com
```

## Install Git

The SonarScanner is the scanner to use when there is no specific scanner for your build system.

Make a directory to save sonar-scanner file:

```bash
 mkdir "your-scanner-file-name"
```

Install sonar-scanner in sonar-scanner directory with this command:

```bash
 wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.7.0.2747-linux.zip
```

Unzip the downloaded files with this command:

```bash
 unzip sonar-scanner-cli-4.7.0.2747-linux.zip
```

Add enviromental variables in sonar-scanner unzip file's directory:

```bash
 export PATH=$PATH:/$(pwd)/bin
 which "your-scanner-file-name"
```

<!-- Set some configuration for routing to :

```bash
 nc -v 10.8.60.126 9000
``` -->

## Scan a Project in SonarScanner

Clone your repository 

```bash
 git clone "your-repository-name"
```

Go to directory of your repository and adding this command:

```bash
 sonar-scanner \
 -Dsonar.projectKey="your-project-key" \
 -Dsonar.sources=. \
 -Dsonar.host.url=http://10.8.60.126:9000 \
 -Dsonar.login="your-generated-token"
```

>We need to replace the-generated-token with the token from above.

Or you can create a configuration file in your project's root directory called sonar-project.properties

```bash
 # must be unique in a given SonarQube instance
 sonar.projectKey=my:project

 # --- optional properties ---

 # defaults to project key
 #sonar.projectName=My project
 # defaults to 'not provided'
 #sonar.projectVersion=1.0
 
 # Path is relative to the sonar-project.properties file. Defaults to .
 #sonar.sources=.
 
 # Encoding of the source code. Default is default system encoding
 #sonar.sourceEncoding=UTF-8
```

After executing the command, the results will be available on the Projects dashboard – at http://localhost:9000.