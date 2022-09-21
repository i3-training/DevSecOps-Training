https://snapcraft.io/install/ngrok/rhel
https://www.unixcloudfusion.in/2022/07/solved-too-early-for-operation-device.html
https://dashboard.ngrok.com/get-started/your-authtoken
https://dashboard.ngrok.com/get-started/setup


docker
cd /etc/
ls -la
cat passwd
cd
cat /etc/gro
cat /etc/group

Because Trigger jenkins pipeline job by scm change requires a public IP. There is a way to forward local port to public using ngrok.

## Install ngrok
There is a way to install ngrok on Linux and RedHat

- ### Install ngrox on Linux/Ubuntu via Apt
``` bash
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null && echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list && sudo apt update && sudo apt install ngrok
```

Run ngrok 
``` bash 
ngrok http 80
```
- ### Install ngrox on RedHat via TGZ file
Open this page -> [Download Ngrok](https://ngrok.com/download)
- CLick right and copy link
![trigger-jenkins](/images/trigger-1.png)
- Run command below on your terminal
``` bash
 wget https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz
```
- Then extract ngrok from the terminal
``` bash
sudo tar xvzf ngrok-v3-stable-linux-amd64.tgz -C /usr/local/bin
``` 
- Run ngrok
``` bash
ngrok http 80
```