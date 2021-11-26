## Working with actions

In terms of a secure and efficient use of GitHub actions it will be highly recommended to follow this **best practices**

### Repository security

Access to code levels have to be restricted for only specific use to users, it can be set at
 - Repository
 - Organization
 - Enterprise

Permission levels resume

![imagen](https://user-images.githubusercontent.com/87127801/143549214-0b9cced9-8306-4474-9144-0b6d6e733d81.png)

### Trace changes
Changes in a repo can be checked through Git commit history.

Also, It is posible in a **organization** from Audit log section of Settings to have a living report of all changes happening with

 - Access
 - Secrets
 - Access Tokens
 - OAuth grants
 - Enabling features
 - Etc.

![imagen](https://user-images.githubusercontent.com/87127801/143549946-bd12c590-b865-4738-b020-5bfe80693dc7.png)

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
