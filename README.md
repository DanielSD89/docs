# GitHub Best Practices

## Words matter

*Workflows*
Execute one or more **Actions**
Worflows triggered by events:
 - Push
 - Creating an issue
 - Release
 
Exectute on a runner

*Actions*
Steps in the **workflows**
Basis: Run a shell script

Create your own
Use an existing one from the marketplace

# Repository security

## Access to code


 **Who has access?**
Access levels can be set at:

 - Repository
 - Organization
 - Enterprise

Permission levels

![imagen](https://user-images.githubusercontent.com/87127801/143549214-0b9cced9-8306-4474-9144-0b6d6e733d81.png)

**Your code/repo - trace changes**
Who made changes:

 - Code: Git commit history
 - Everything **around** your code is in the **audit log**

**Your code/repo - trace changes (org level)**
Audit log:

 - Access
 - Secrets
 - Access Tokens
 - OAuth grants
 - Enabling features
 - Etc.

![imagen](https://user-images.githubusercontent.com/87127801/143549946-bd12c590-b865-4738-b020-5bfe80693dc7.png)

## Workflows secrets

![imagen](https://user-images.githubusercontent.com/87127801/143551236-683bccc8-3216-49c2-9bf8-dd29c85adf07.png)

Encrypted client side before reaching GitHub:

 - Encrypted with the public key for your org or repo (created and stored by GitHub)
 - Used when using the UI
 - Encrypt yourself before posting to the REST API

Secrets are **not** shared to forked repositories

**Who has access to your secrets?**
For creating at **repo** level: Repository Owner access
For creating at **org** level: Admin access to the org

 - Set an access policy for the secrets
	 - All repositories
	 - Private repositories
	 - Only selected repositories

Encrypted until used, then injected as:

 - An environment variable
 - Direct input

Wil be redacted in logs

Don't use structured data (like json): hard to redact

 - Actions can do anything with secrets!
 - **Anyone with access to the Action Logs** should be considered to have access to your secrets

## Your code/repo

Anything in your repository:

 - Workflows files
 - Shell scripts
 - Your own code
 - Dependencies
	 - Packages
	 - Containers

**Best practices**:

 - Static code analysis
	 - Check your own code!

 - Third party dependency scanning
	 - 99% of your code, is not yours:
		 - Scan for known vulnerabilities

	- Keep your dependencies up to date!

# Runners and security
## Workflows Runners

Action execute on runners

![imagen](https://user-images.githubusercontent.com/87127801/143554650-5aeaeb1b-8fb8-4b8a-91e1-fccc444feb05.png)

 - Self hosted
	 - Cloud / On premises hosted by yourself
	 - OS + Tools update = YOUR responsibility
	 - Enables specific environment setup
	 - No usage limits

![imagen](https://user-images.githubusercontent.com/87127801/143554716-ba09bb25-8d01-435e-9a7f-4ee95013a171.png)

 - GitHub hosted
	 - OS + Tools update = GitHub's responsibility
	 - Per minute rating applies after free minutes
	 - Clean execution environment with every run

**Security**	 

 - Environment scope
	 - Network
	 - Shared state between runs
- User: limits its access!

**Best practice: Run the action inside of a container**

![imagen](https://user-images.githubusercontent.com/87127801/143555445-7e3ab99b-0878-43f2-a812-00bf0317e6c5.png)

![imagen](https://user-images.githubusercontent.com/87127801/143555728-3cdf17f1-1a3c-490a-b1ea-040df17e5a8b.png)

**Best practice: Don't use self hosted runners for public repositories**

Example:

 - Your repo
 - New fork
 - Adds malicious code
 - Create pull request to your repo
 - Workflow is executed on your self hosted runner?

## Persisting data between runs
Run1:

 - Download dependencies
 - Build the code
 - Somehow overwrite the dependency cache

Run2:

 - Use cached dependencies
 - Build the code
 - Malicious dependency in build artefact

## Workflow runners - Best practice

Don't share runners (and machines!) between repositories:

 - Run1 can influence Run2

Risks:

 - Malicious programs
 - Escaping the runner sandbox
 - Exposing access to the (network) environment
 - Persisting unwanted or dangerous data

# Runners and security

## Actions

Marketplace or by direct url

![imagen](https://user-images.githubusercontent.com/87127801/143569500-14a0fbce-7795-4a64-bea3-384fc3916e2a.png)

![imagen](https://user-images.githubusercontent.com/87127801/143569592-ede99095-4ae9-4bf8-b8ee-6ae631a1c269.png)

![imagen](https://user-images.githubusercontent.com/87127801/143569670-88531087-f74c-434d-bc09-794e4e82638a.png)

## Actions and security
![imagen](https://user-images.githubusercontent.com/87127801/143569929-5d85e953-18c8-4c80-bb1d-1deab364feab.png)

## Protective measures

Manually:

 1. Check the action repo code before use
 2. Check its container images and dependencies before use

Only use actions listed in the marketplace?

 - There is no real verification process for it :(

![imagen](https://user-images.githubusercontent.com/87127801/143570606-448ab361-43b1-4d33-a862-b3f07d0310d6.png)

**Verified Creator**
Verification process:

 - GitHub Profile information is presente and accurate
 - Two factor authentication is on for the organization
 - Domain verification through a txt record

**Limiting actions altogether**

![imagen](https://user-images.githubusercontent.com/87127801/143572278-03e13c7e-fee7-41eb-9fc3-d92b6b6d078e.png)

![imagen](https://user-images.githubusercontent.com/87127801/143573867-91e24108-205f-4012-83b5-010478398f7d.png)

Pin the action version:
uses:gaurav-nelson/github-action-markdown-link-check@v1
uses:gaurav-nelson/github-action-markdown-link-check@v1.0.1

**Best practice**: Pin the Action's commit SHA:
uses:gaurav-nelson/github-action-markdown-link-check@44a942b...
