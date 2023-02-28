# Scanning Ssecret on Repository using Trivy

To scan a repository for secrets using Trivy, you can use the following steps:

1. Clone the repository to your local machine using the command line:

```bash
git clone <repo-url>
```

2. Navigate to the cloned repository directory:

```bash
cd <repo-directory>
```

3. Install Trivy on your system, as described in my previous response.

4. Run Trivy to scan the repository for secrets, using the --severity option to specify the severity level of vulnerabilities to include in the report:

```bash
trivy fs . --severity HIGH,CRITICAL --ignore-unfixed
```

Note: --severity option specifies the severity level of vulnerabilities to include in the report. The --ignore-unfixed option ignores unfixed vulnerabilities.

Run Trivy to scan the remote repository for secrets, using the trivy repo command and the URL of the remote repository:

```bash
trivy repo --severity HIGH,CRITICAL https://github.com/<username>/<repository>
```

Trivy will scan the repository for secrets and report any vulnerabilities it finds. The report will include the severity level of each vulnerability, as well as any recommended actions to fix the vulnerability.