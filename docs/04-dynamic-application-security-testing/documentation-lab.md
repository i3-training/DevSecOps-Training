# Guide Lab DAST

## Pull Image Zap proxy
First, we need to pull ZAP’s stable docker image.
```
docker pull owasp/zap2docker-stable
```
Check Help Zap baseline
```
docker run --rm owasp/zap2docker-stable zap-baseline.py --help
```

## Run the Scanner
Running Zap Baseline
```
docker run --rm owasp/zap2docker-stable zap-baseline.py -t http://gosip-app-dev.apps.lab.i3datacenter.my.id
```

Result
```
Using the Automation Framework
Total of 9 URLs
PASS: Vulnerable JS Library (Powered by Retire.js) [10003]
PASS: In Page Banner Information Leak [10009]
PASS: Cookie No HttpOnly Flag [10010]
PASS: Cookie Without Secure Flag [10011]
PASS: Re-examine Cache-control Directives [10015]
PASS: Cross-Domain JavaScript Source File Inclusion [10017]
PASS: Content-Type Header Missing [10019]
PASS: Information Disclosure - Debug Error Messages [10023]
PASS: Information Disclosure - Sensitive Information in URL [10024]
PASS: Information Disclosure - Sensitive Information in HTTP Referrer Header [10025]
PASS: HTTP Parameter Override [10026]
PASS: Information Disclosure - Suspicious Comments [10027]
PASS: Open Redirect [10028]
PASS: Cookie Poisoning [10029]
PASS: User Controllable Charset [10030]
PASS: Viewstate [10032]
PASS: Directory Browsing [10033]
PASS: Heartbleed OpenSSL Vulnerability (Indicative) [10034]
PASS: Strict-Transport-Security Header [10035]
PASS: Server Leaks Information via "X-Powered-By" HTTP Response Header Field(s) [10037]
PASS: X-Backend-Server Header Information Leak [10039]
PASS: Secure Pages Include Mixed Content [10040]
PASS: HTTP to HTTPS Insecure Transition in Form Post [10041]
PASS: HTTPS to HTTP Insecure Transition in Form Post [10042]
PASS: User Controllable JavaScript Event (XSS) [10043]
PASS: Big Redirect Detected (Potential Sensitive Information Leak) [10044]
PASS: Retrieved from Cache [10050]
PASS: X-ChromeLogger-Data (XCOLD) Header Information Leak [10052]
PASS: CSP [10055]
PASS: X-Debug-Token Information Leak [10056]
PASS: Username Hash Found [10057]
PASS: X-AspNet-Version Response Header [10061]
PASS: PII Disclosure [10062]
PASS: Timestamp Disclosure [10096]
PASS: Hash Disclosure [10097]
PASS: Cross-Domain Misconfiguration [10098]
PASS: Weak Authentication Method [10105]
PASS: Reverse Tabnabbing [10108]
PASS: Modern Web Application [10109]
PASS: Dangerous JS Functions [10110]
PASS: Absence of Anti-CSRF Tokens [10202]
PASS: Private IP Disclosure [2]
PASS: Session ID in URL Rewrite [3]
PASS: Script Passive Scan Rules [50001]
PASS: Stats Passive Scan Rule [50003]
PASS: Insecure JSF ViewState [90001]
PASS: Java Serialization Object [90002]
PASS: Sub Resource Integrity Attribute Missing [90003]
PASS: Charset Mismatch [90011]
PASS: Application Error Disclosure [90022]
PASS: WSDL File Detection [90030]
PASS: Loosely Scoped Cookie [90033]
WARN-NEW: Missing Anti-clickjacking Header [10020] x 4
        http://gosip-app-dev.apps.lab.i3datacenter.my.id (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/login (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/register (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/login (200 OK)
WARN-NEW: X-Content-Type-Options Header Missing [10021] x 6
        http://gosip-app-dev.apps.lab.i3datacenter.my.id (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/login (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/register (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/static/css/bootstrap.min.css (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/static/css/login.css (200 OK)
WARN-NEW: User Controllable HTML Element Attribute (Potential XSS) [10031] x 1
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/login (200 OK)
WARN-NEW: Server Leaks Version Information via "Server" HTTP Response Header Field [10036] x 9
        http://gosip-app-dev.apps.lab.i3datacenter.my.id (302 Found)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/login (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/register (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/robots.txt (404 Not Found)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/sitemap.xml (404 Not Found)
WARN-NEW: Content Security Policy (CSP) Header Not Set [10038] x 6
        http://gosip-app-dev.apps.lab.i3datacenter.my.id (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/login (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/register (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/robots.txt (404 Not Found)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/sitemap.xml (404 Not Found)
WARN-NEW: Non-Storable Content [10049] x 10
        http://gosip-app-dev.apps.lab.i3datacenter.my.id (302 Found)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/register (302 Found)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/login (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/register (200 OK)
WARN-NEW: Cookie without SameSite Attribute [10054] x 5
        http://gosip-app-dev.apps.lab.i3datacenter.my.id (302 Found)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/login (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/login (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/register (302 Found)
WARN-NEW: Permissions Policy Header Not Set [10063] x 6
        http://gosip-app-dev.apps.lab.i3datacenter.my.id (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/login (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/register (200 OK)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/robots.txt (404 Not Found)
        http://gosip-app-dev.apps.lab.i3datacenter.my.id/sitemap.xml (404 Not Found)
FAIL-NEW: 0     FAIL-INPROG: 0  WARN-NEW: 8     WARN-INPROG: 0  INFO: 0 IGNORE: 0       PASS: 52
```


### Running Zap Baseline
If you want the output in JSON format, you can use the -J option
```
sudo su
docker run -u root -w /zap -v $(pwd):/zap/wrk:rw --rm owasp/zap2docker-stable zap-baseline.py -t http://gosip-app-dev.apps.lab.i3datacenter.my.id -J zap-output.json -r zap-output.html
```
 

### Running Zap on jenkins CI/CD
Jenkinsfile : zap-baseline-jenkinsfile
