## Working with actions

In terms of a secure and efficient use of GitHub actions it will be highly recommended to follow this **best practices**

### Workflows secrets
Notice that secrets are **not** shared to forked repositories.

Access and manage of secrets works this way:
- For creating at **repo** level: Repository Owner access
- For creating at **org** level: Admin access to the org

Access policy for the secrets
 - All repositories
 - Private repositories
 - Only selected repositories

Don't use structured data (like json): hard to redact

 - Actions can do anything with secrets
 - **Anyone with access to the Action Logs** should be considered to have access to your secrets

### Static code analysis
All code has to be checked through scans for know their possible vulnerabilities.
Always keep dependencies up to date

### Workflows Runners

 A best practice is to run the action inside of a container

![imagen](https://user-images.githubusercontent.com/87127801/143555445-7e3ab99b-0878-43f2-a812-00bf0317e6c5.png)

![imagen](https://user-images.githubusercontent.com/87127801/143555728-3cdf17f1-1a3c-490a-b1ea-040df17e5a8b.png)

As well as **don't use self hosted runners for public repositories**

### Persisting data between runs
Don't share runners (and machines!) between repositories:

Risks:

 - Malicious programs
 - Escaping the runner sandbox
 - Exposing access to the (network) environment
 - Persisting unwanted or dangerous data


### Runners and security

Use actions in workflows from marketplace or by direct url

![imagen](https://user-images.githubusercontent.com/87127801/143569500-14a0fbce-7795-4a64-bea3-384fc3916e2a.png)

![imagen](https://user-images.githubusercontent.com/87127801/143569592-ede99095-4ae9-4bf8-b8ee-6ae631a1c269.png)

![imagen](https://user-images.githubusercontent.com/87127801/143569670-88531087-f74c-434d-bc09-794e4e82638a.png)

### Protective measures
Manually:
- Check the action repo code before use
- Check its container images and dependencies before use
- Preferently use actions from verified creators

### Limiting actions altogether
Using a whitelist to manage actions use is highly recommended

![imagen](https://user-images.githubusercontent.com/87127801/143572278-03e13c7e-fee7-41eb-9fc3-d92b6b6d078e.png)

**Best practice** for using an action is to pin the action's commit SHA:
``` yaml
uses:gaurav-nelson/github-action-markdown-link-check@44a942b...
```

-------



## Workflow attack vectors

- Forks of public repos
- Common fields

### Forks of public repos
![imagen](https://user-images.githubusercontent.com/87127801/143840292-5393a6ef-d531-4975-8618-e232597027e2.png)

push and pull_request - Safe, runs on merge commit, read only access
pull_request_target - High risks! Runs on the target, has read + write access and can access secrets

### Pull Requests

${{secrets.GITHUB_TOKEN}}

![imagen](https://user-images.githubusercontent.com/87127801/143841233-74d91277-5b2f-4933-b8c8-6768651dc102.png)

![imagen](https://user-images.githubusercontent.com/87127801/143841316-1aa51873-caa3-443e-86a8-c8e30dfd8841.png)

### Common fields

![imagen](https://user-images.githubusercontent.com/87127801/143841426-13ef155b-9647-4898-92c3-f89f11d3f746.png)

```yaml
- name: Check title
  run: |
    title="${{ github.event.issue.title }}"
    if [[ ! $title =~ ^.*:\ .*$ ]]; then
      echo "Bad issue title"
      exit 1
    fi
```
### Remdiation

```yaml
- name: print title
  env:
    TITLE: ${{ github.event.issue.title }}
  run: echo `$TITLE´



