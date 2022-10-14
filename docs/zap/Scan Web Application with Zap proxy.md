#OWASP ZAP

##What is ZAP?
Open Web Application Security Project (OWASP) Zed Attack Proxy (ZAP) is an open-source and free penetration testing tool.

##Install ZAP on RHEL 8
- Download ZAP
```bash
wget https://github.com/zaproxy/zaproxy/releases/download/v2.11.1/ZAP_2_11_1_unix.sh
```

- Install ZAP
```bash
sh ZAP_2_11_1_unix.sh
```

- Make sure ZAP is installed on the machine with help command
```bash
zap.sh -help
```

- Finish.

##Set IP Target
- Before we start scanning using ZAP, we must set the IP hosts because the DNS isn't public
```bash
vim /etc/hosts
```

- Insert this
```bash
10.8.60.50 gosip-app-intern-devsecops-dev.apps.lab.i3datacenter.my.id bodgeit-intern-devsecops-dev.apps.lab.i3datacenter.my.id
```

##Scan With ZAP
```bash
zap.sh -daemon -quickurl http://bodgeit-intern-devsecops-dev.apps.lab.i3datacenter.my.id -quickout $(pwd)/report.html
```

Note : 
-daemon                 : Starts ZAP in daemon mode, i.e. without a UI
-quickurl <target url>  : The URL to attack
-quickout <filename>    : The file to write the HTML/JSON/MD/XML results to (based on the file extension)

Once scanning success, it should look like the following :
```bash
...
968562 [Thread-6] INFO  org.parosproxy.paros.core.scanner.Scanner - scanner completed in 857.949s
Writing results to /root/zap/reportzap.html
973452 [ZAP-daemon] INFO  org.zaproxy.zap.DaemonBootstrap - ZAP is now listening on localhost:8080
```

And we can terminated it with Ctrl + C.

##Scan With Docker
- Pull Image ictu/zap2docker-weekly
```bash
docker pull ictu/zap2docker-weekly
```

###Set IP in container
- Create a hosts file that will replace the original container hosts
```bash
vim /opt/hosts_zap
```

- Insert this
```bash
10.8.60.50 gosip-app-intern-devsecops-dev.apps.lab.i3datacenter.my.id
```

###Baseline Scan
Baseline scan is a time limited spider (by default, 1 minute) which reports issues of the passive scanning.

- Usage
```bash
docker run -t <target> zap-baseline.py -t <target> [options]
```

- Example Command
```bash
docker run --rm -v $(pwd):/zap/wrk:rw -v /opt/hosts_zap:/etc/hosts -t ictu/zap2docker-weekly zap-baseline.py -I -j \
-t http://gosip-app-intern-devsecops-dev.apps.lab.i3datacenter.my.id/ \
-r baselinereport.html \
-J baselinereport.json \
-g baselinereport.conf \
--hook=/zap/auth_hook.py \
-z "auth.loginurl=http://gosip-app-intern-devsecops-dev.apps.lab.i3datacenter.my.id/login \
auth.username="user" \
auth.password="password""
```

Once scanning success, it should look like the following :
```bash
...
WARN-NEW: Cookie without SameSite Attribute [10054] x 2
        http://gosip-app-intern-devsecops-dev.apps.lab.i3datacenter.my.id/login (200 OK)
        http://gosip-app-intern-devsecops-dev.apps.lab.i3datacenter.my.id/register (302 FOUND)
FAIL-NEW: 0     FAIL-INPROG: 0  WARN-NEW: 4     WARN-INPROG: 0  INFO: 0 IGNORE: 0       PASS: 29
```

###Full Scan
Full scan is a full spider (by default, no time limit) which reports issues of the full active scanning and passive scanning.

- Usage
```bash
docker run -t <target> zap-full-scan.py -t <target> [options]
```

- Example Command
```bash
docker run --rm -v $(pwd):/zap/wrk:rw -v /opt/hosts_zap:/etc/hosts -t ictu/zap2docker-weekly zap-full-scan.py -I -j \
-t http://gosip-app-intern-devsecops-dev.apps.lab.i3datacenter.my.id/ \
-r fullreport.html \
-J fullreport.json \
-g fullreport.conf \
--hook=/zap/auth_hook.py \
-z "auth.loginurl=http://gosip-app-intern-devsecops-dev.apps.lab.i3datacenter.my.id/login \
auth.username="user" \
auth.password="password""
```

Once scanning success, it should look like the following :
```bash
...
WARN-NEW: Cookie without SameSite Attribute [10054] x 2
        http://gosip-app-intern-devsecops-dev.apps.lab.i3datacenter.my.id/login (200 OK)
        http://gosip-app-intern-devsecops-dev.apps.lab.i3datacenter.my.id/register (302 FOUND)
FAIL-NEW: 0     FAIL-INPROG: 0  WARN-NEW: 4     WARN-INPROG: 0  INFO: 0 IGNORE: 0       PASS: 52
```

###API Scan
API scan is a full scan agains API defined using OpenAPI/Swagger or SOAP.

- Install addons soap & openapi
```bash
zap.sh -daemon -addoninstall soap -addoninstall openapi
```

- Open localhost:8090/UI/soap & localhost:8090/UI/openapi on our fav browser

- Usage
```bash
docker run -t <target> zap-api-scan.py -t <target> -f <format>[options]
```

- Example Command
```bash
docker run -t owasp/zap2docker-weekly zap-api-scan.py -t \  
    https://www.example.com/openapi.json -f openapi 
```