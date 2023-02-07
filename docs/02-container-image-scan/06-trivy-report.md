# Trivy Report

## Table Format (Default)

When using Trivy from terminal the default output is table view printed on your cli.

```bash
$ trivy image python:3.4-alpine

2019-05-16T01:20:43.180+0900    INFO    Updating vulnerability database...
2019-05-16T01:20:53.029+0900    INFO    Detecting Alpine vulnerabilities...

python:3.4-alpine3.9 (alpine 3.9.2)
===================================
Total: 1 (UNKNOWN: 0, LOW: 0, MEDIUM: 1, HIGH: 0, CRITICAL: 0)

+---------+------------------+----------+-------------------+---------------+--------------------------------+
| LIBRARY | VULNERABILITY ID | SEVERITY | INSTALLED VERSION | FIXED VERSION |             TITLE              |
+---------+------------------+----------+-------------------+---------------+--------------------------------+
| openssl | CVE-2019-1543    | MEDIUM   | 1.1.1a-r1         | 1.1.1b-r1     | openssl: ChaCha20-Poly1305     |
|         |                  |          |                   |               | with long nonces               |
+---------+------------------+----------+-------------------+---------------+--------------------------------+

```

you can manipulate this and save the report using linux stdout function, command below is example of how you can do it.

```bash
trivy image python:3.4-alpine > scan-report.txt
```

## JSON Format

You also can use JSON to export scan result, you can use json if you wish to send scan report to third party tools for further use. command below show you how to create output as a json file.

```bash
trivy image -f json -o results.json golang:1.12-alpine
```

Example JSON report:

```json
[
  {
    "Target": "php-app/composer.lock",
    "Vulnerabilities": null
  },
  {
    "Target": "node-app/package-lock.json",
    "Vulnerabilities": [
      {
        "VulnerabilityID": "CVE-2018-16487",
        "PkgName": "lodash",
        "InstalledVersion": "4.17.4",
        "FixedVersion": "\u003e=4.17.11",
        "Title": "lodash: Prototype pollution in utilities function",
        "Description": "A prototype pollution vulnerability was found in lodash \u003c4.17.11 where the functions merge, mergeWith, and defaultsDeep can be tricked into adding or modifying properties of Object.prototype.",
        "Severity": "HIGH",
        "References": [
          "https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-16487",
        ]
      }
    ]
  },
  {
    "Target": "trivy-ci-test (alpine 3.7.1)",
    "Vulnerabilities": [
      {
        "VulnerabilityID": "CVE-2018-16840",
        "PkgName": "curl",
        "InstalledVersion": "7.61.0-r0",
        "FixedVersion": "7.61.1-r1",
        "Title": "curl: Use-after-free when closing \"easy\" handle in Curl_close()",
        "Description": "A heap use-after-free flaw was found in curl versions from 7.59.0 through 7.61.1 in the code related to closing an easy handle. ",
        "Severity": "HIGH",
        "References": [
          "https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-16840",
        ]
      },
      {
        "VulnerabilityID": "CVE-2019-3822",
        "PkgName": "curl",
        "InstalledVersion": "7.61.0-r0",
        "FixedVersion": "7.61.1-r2",
        "Title": "curl: NTLMv2 type-3 header stack buffer overflow",
        "Description": "libcurl versions from 7.36.0 to before 7.64.0 are vulnerable to a stack-based buffer overflow. ",
        "Severity": "HIGH",
        "References": [
          "https://curl.haxx.se/docs/CVE-2019-3822.html",
          "https://lists.apache.org/thread.html/8338a0f605bdbb3a6098bb76f666a95fc2b2f53f37fa1ecc89f1146f@%3Cdevnull.infra.apache.org%3E"
        ]
      },
      {
        "VulnerabilityID": "CVE-2018-16839",
        "PkgName": "curl",
        "InstalledVersion": "7.61.0-r0",
        "FixedVersion": "7.61.1-r1",
        "Title": "curl: Integer overflow leading to heap-based buffer overflow in Curl_sasl_create_plain_message()",
        "Description": "Curl versions 7.33.0 through 7.61.1 are vulnerable to a buffer overrun in the SASL authentication code that may lead to denial of service.",
        "Severity": "HIGH",
        "References": [
          "https://github.com/curl/curl/commit/f3a24d7916b9173c69a3e0ee790102993833d6c5",
        ]
      },
      {
        "VulnerabilityID": "CVE-2018-19486",
        "PkgName": "git",
        "InstalledVersion": "2.15.2-r0",
        "FixedVersion": "2.15.3-r0",
        "Title": "git: Improper handling of PATH allows for commands to be executed from the current directory",
        "Description": "Git before 2.19.2 on Linux and UNIX executes commands from the current working directory (as if '.' were at the end of $PATH) in certain cases involving the run_command() API and run-command.c, because there was a dangerous change from execvp to execv during 2017.",
        "Severity": "HIGH",
        "References": [
          "https://usn.ubuntu.com/3829-1/",
        ]
      },
      {
        "VulnerabilityID": "CVE-2018-17456",
        "PkgName": "git",
        "InstalledVersion": "2.15.2-r0",
        "FixedVersion": "2.15.3-r0",
        "Title": "git: arbitrary code execution via .gitmodules",
        "Description": "Git before 2.14.5, 2.15.x before 2.15.3, 2.16.x before 2.16.5, 2.17.x before 2.17.2, 2.18.x before 2.18.1, and 2.19.x before 2.19.1 allows remote code execution during processing of a recursive \"git clone\" of a superproject if a .gitmodules file has a URL field beginning with a '-' character.",
        "Severity": "HIGH",
        "References": [
          "http://www.securitytracker.com/id/1041811",
        ]
      }
    ]
  },
  {
    "Target": "python-app/Pipfile.lock",
    "Vulnerabilities": null
  },
  {
    "Target": "ruby-app/Gemfile.lock",
    "Vulnerabilities": null
  },
  {
    "Target": "rust-app/Cargo.lock",
    "Vulnerabilities": null
  }
]
```

VulnerabilityID, PkgName, InstalledVersion, and Severity in Vulnerabilities are always filled with values, but other fields might be empty.

## Template HTML Report

To make it easier to read the results of the reports obtained from Trivy, we can export the analysis results from trivy into html format. We need to first create an template file to be use as reporting, in this example we use html template from trivy default template, you can find out more about the template [here.](https://github.com/aquasecurity/trivy/blob/main/contrib/html.tpl)

if you installed trivy from RPM or other package manager you can use default template located on `/usr/local/share/trivy/templates`.

command below show how to create html reporting from a scan

```bash
# The following example shows use of default HTML template when Trivy is installed using rpm.
trivy image --format template --template "@/usr/local/share/trivy/templates/html.tpl" -o report.html golang:1.12-alpine

# if You create your own template you can use this command instead
trivy image --format template --template "@/your/custom/template/html.tpl" -o report.html golang:1.12-alpine
```
