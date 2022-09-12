# Jenkins Installation

## Requirements
- System: RHEL 8
- Software: Jenkins v.2.361.1  
- 256 MB of RAM, 1 GB+ recommended
- 1 GB of drive space (although 10 GB is a recommended minimum if running Jenkins as a Docker container)

## Install Jenkins on RHEL 8
1. Install Java on RHEL 8
```bash
sudo yum -y install java-11-openjdk-devel
```
2. Check Java version
```bash 
java -version
```
3. Adding Jenkins Repository
Install wget package is no installed already 
``` bash
sudo yum -y install wget
```
Download and enable Jenkins Repository
``` bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
```
Import Jenkins GPG Key
``` bash
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
```
4. Install Jenkins
``` bash
sudo yum -y install jenkins
```
5. Start and Enable Jenkins Service
After installation, start and enable Jenkins service
``` bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
sudo systemctl status jenkins
```
6. Configure Firewall
Jenkins is works on port 8080 so we need to allow conection on that port by adding it in firewall
``` bash
sudo firewall-cmd --add-port=8080/tcp --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```
7. Configure Jenkins on RHEL 
Browser to the URL **server-ip:8080**

you will see page below asking for Admin password to Unlock Jenkins, when you browse the URL first time.

![getting-started](/images/getting-started.png)

A path is also display on the page in which the password is saved, so go to your terminal and issue following command to get the password.

> cat /var/lib/jenkins/secrets/initialAdminPassword

![unlock-jenkins](/images/unlock-jenkins.png)

copy and paste it into the "Adminstrator password" box and click Continue

![unlock-jenkins-password](/images/unlock-jenkins-password.png)

So you will be asked if you want to install the suggested plugins or install additional plugins. I choose “install the suggested plugins” option you can choose your desired and the installation process will start immediately.

![customize-jenkins](/images/customize-jenkins.png)

![getting-started-2](/images/getting-started-2.png)

Now, create an admin account which will be used to manage the Jenkins server

![admin-user](/images/admin-user.png)

Then, set a URL or leave it as default and click on Save and Finish button.

![instance-configuration](/images/instance-configuration.png)

Evrything has been done, click " Start using Jenkins" button and you will redirect to the Jenkins dashboard.

![jenkins](/images/jenkins.png)