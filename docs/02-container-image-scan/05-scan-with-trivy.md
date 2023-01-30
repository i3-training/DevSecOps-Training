# Scan Image and Filesystem With Trivy

## Scanning Container Images

Trivy can do a direct scan of an existing image on the docker hub accompanied by a version of the image
Install now.

```bash
trivy image python:3.9.14-slim-bullseye
```

Trivy can be used to scan images in the local repository.

```bash
trivy image [YOUR_IMAGE_NAME]
```

you also can filter by severity this allow you to only show or cancel pipeline based on severity you define.

```bash
trivy image --severity HIGH,CRITICAL python:3.9.14-slim-bullseye
```

command above will only show High and Critical vulnerability

## Vulnerability and Misconfiguration scanning

The difference between fs and config subcommand is that fs can detect both vulnerabilities and misconfiguration at the same time.

You have to specify --security-checks vuln,config to enable vulnerability and misconfiguration detection.

```bash
$ ls myapp/
Dockerfile Pipfile.lock
$ trivy fs --security-checks vuln,config --severity HIGH,CRITICAL myapp/
2021-07-09T12:03:27.564+0300    INFO    Detected OS: unknown
2021-07-09T12:03:27.564+0300    INFO    Number of language-specific files: 1
2021-07-09T12:03:27.564+0300    INFO    Detecting pipenv vulnerabilities...
2021-07-09T12:03:27.566+0300    INFO    Detected config files: 1

Pipfile.lock (pipenv)
=====================
Total: 1 (HIGH: 1, CRITICAL: 0)

+----------+------------------+----------+-------------------+---------------+---------------------------------------+
| LIBRARY  | VULNERABILITY ID | SEVERITY | INSTALLED VERSION | FIXED VERSION |                 TITLE                 |
+----------+------------------+----------+-------------------+---------------+---------------------------------------+
| httplib2 | CVE-2021-21240   | HIGH     | 0.12.1            | 0.19.0        | python-httplib2: Regular              |
|          |                  |          |                   |               | expression denial of                  |
|          |                  |          |                   |               | service via malicious header          |
|          |                  |          |                   |               | -->avd.aquasec.com/nvd/cve-2021-21240 |
+----------+------------------+----------+-------------------+---------------+---------------------------------------+

Dockerfile (dockerfile)
=======================
Tests: 23 (SUCCESSES: 22, FAILURES: 1, EXCEPTIONS: 0)
Failures: 1 (HIGH: 1, CRITICAL: 0)

+---------------------------+------------+----------------------+----------+------------------------------------------+
|           TYPE            | MISCONF ID |        CHECK         | SEVERITY |                 MESSAGE                  |
+---------------------------+------------+----------------------+----------+------------------------------------------+
| Dockerfile Security Check |   DS002    | Image user is 'root' |   HIGH   | Last USER command in                     |
|                           |            |                      |          | Dockerfile should not be 'root'          |
|                           |            |                      |          | -->avd.aquasec.com/appshield/ds002       |
+---------------------------+------------+----------------------+----------+------------------------------------------+

```

## Show origins of vulnerable dependencies

Modern software development relies on the use of third-party libraries. Third-party dependencies also depend on others so a list of dependencies can be represented as a dependency graph. In some cases, vulnerable dependencies are not linked directly, and it requires analyses of the tree. To make this task simpler Trivy can show a dependency origin tree with the --dependency-tree flag. This flag is only available with the --format table flag.

In table output, it looks like:

```bash
$ trivy fs --severity HIGH,CRITICAL --dependency-tree /path/to/your_node_project

package-lock.json (npm)
=======================
Total: 2 (HIGH: 1, CRITICAL: 1)

┌──────────────────┬────────────────┬──────────┬───────────────────┬───────────────┬────────────────────────────────────────────────────────────┐
│     Library      │ Vulnerability  │ Severity │ Installed Version │ Fixed Version │                           Title                            │
├──────────────────┼────────────────┼──────────┼───────────────────┼───────────────┼────────────────────────────────────────────────────────────┤
│ follow-redirects │ CVE-2022-0155  │ HIGH     │ 1.14.6            │ 1.14.7        │ follow-redirects: Exposure of Private Personal Information │
│                  │                │          │                   │               │ to an Unauthorized Actor                                   │
│                  │                │          │                   │               │ https://avd.aquasec.com/nvd/cve-2022-0155                  │
├──────────────────┼────────────────┼──────────┼───────────────────┼───────────────┼────────────────────────────────────────────────────────────┤
│ glob-parent      │ CVE-2020-28469 │ CRITICAL │ 3.1.0             │ 5.1.2         │ nodejs-glob-parent: Regular expression denial of service   │
│                  │                │          │                   │               │ https://avd.aquasec.com/nvd/cve-2020-28469                 │
└──────────────────┴────────────────┴──────────┴───────────────────┴───────────────┴────────────────────────────────────────────────────────────┘

Dependency Origin Tree
======================
package-lock.json
├── follow-redirects@1.14.6, (HIGH: 1, CRITICAL: 0)
│   └── axios@0.21.4
└── glob-parent@3.1.0, (HIGH: 0, CRITICAL: 1)
    └── chokidar@2.1.8
        └── watchpack-chokidar2@2.0.1
            └── watchpack@1.7.5
                └── webpack@4.46.0
                    └── cra-append-sw@2.7.0

```

You also can scan a container image to find what dependencies being use.

```bash
trivy image --severity CRITICAL -f table --dependency-tree nginx:1.9.14-alpine
```

## Vulnerability DB

### Skip update of vulnerability DB

Trivy downloads its vulnerability database every 12 hours when it starts operating. This is usually fast, as the size of the DB is only 10~30MB. But if you want to skip even that, use the --skip-db-update option.

```bash
trivy image --skip-db-update python:3.4-alpine3.9
```

### Only download vulnerability database

You can also ask Trivy to simply retrieve the vulnerability database. This is useful to initialize workers in Continuous Integration systems.

```bash
trivy image --download-db-only
```

### Lightweight DB

The lightweight DB doesn't contain vulnerability detail such as descriptions and references. Because of that, the size of the DB is smaller and the download is faster.

This option is useful when you don't need vulnerability details and is suitable for CI/CD. To find the additional information, you can search vulnerability details on the [NVD website](https://nvd.nist.gov/vuln/search).
