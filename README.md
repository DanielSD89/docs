# Security

> … if we don’t do this, people simply won’t be willing — or able — to take
advantage of all the other great work we do. Trustworthy Computing is the
highest priority.
>
> Bill Gates 2002.

On the 15th of January 2002, Bill Gates set about writing the now-famous
Trustworthy Computing memo which set about the direction Microsoft was about to
take with regards to how they developed and shipped code. The approach Santander
is taking with regards to Trustworthy Computing looks at how we can ensure the
code we push is as secure as possible using a variety of tools and approaches.

This guide is about the journey we are all taking with regards to security.

## Table Of Content

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

* [The Developer IDE](#the-developer-ide)
* [The CI/CD Pipeline](#the-cicd-pipeline)
  * [Dependabot](#dependabot)
  * [Advanced Features](#advanced-features)
    * [Static application security testing (SAST)](#static-application-security-testing-sast)
  * [CodeQL](#codeql)
* [Docker vulnerability scanners](#docker-vulnerability-scanners)
* [Working with actions](#working-with-actions)
* [Teams](#teams)
* [Repositories](#repositories)
* [Secret Scanning](#secret-scanning)
* [Secrets](#secrets)
* [Deploy keys](#deploy-keys)
* [Using secure actions](#using-secure-actions)
  * [First security layer](#first-security-layer)
  * [Second security layer](#second-security-layer)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

The journey our development teams take is really important, we want to move away
from old approaches that introduced security friction, such as tools that take
hours to scan code and therefore stop the pipeline from being agile to instant
feedback on bugs found during a git commit && git push and as such, there are
numerous smaller tools that make up this process.

This can be broken down into three main sections:

1. The developer IDE
2. The CI/CD pipeline
3. The final release

Each of the sections are as important as the others and together should help all
when it comes to writing and slinging code. It is important to stress that there
are no hard and fast rules when it comes to security tools and processes and as
such there is no one tool that does it all.

We are aiming to embrace the onion approach to security: lots of layers.

## The Developer IDE

In an ideal world, when you write code, there should be a function that
automatically scans your code to inform you as to when an insecure function was
just written, or where you aren't validating input or incorrectly assigning a
variable to memory.

## The CI/CD Pipeline

From trying to tackle the supply chain security issues to ensuring committed
code is as secure as possible, here is where most of the research is happening.

For a deeper look at the GitHub Actions Security process, visit the
[working with actions section.](#working-with-actions)

### Dependabot

Dependabot is amazing. Dependabot creates pull requests to keep your
dependencies secure and up-to-date thus ensuring all of your dependencies are
free of vulnerabilities and up-to-date. Best part is that it is automatic,
meaning you don't have to do anything (well, except review the pull request)

Dependabot can be enabled in a repo by including a dependabot.yml file within
the _.github_ folder.

![Dependabot](../resources/dependabot2.png)

Once it finds a dependency that needs updating, you will receive a pull request
and you can even specify who gets these assigned to them.

![Dependabot](../resources/dependabot.png)

Once enabled Dependabot will try to keep all of the dependencies always updated
to the latest version. When a new version of a dependency is released Dependabot
will create a pull request and run all workflows enabled for pull requests. It
will then wait for a developer to merge the pull request or reject it.

![Dependabot](../resources/dependabot3.png)

### Advanced Features

You can interact with DependaBot through the following commands in a
comment in the pull request:

* __@dependabot rebase__ will rebase this PR.
* __@dependabot recreate__ will recreate this PR, overwriting any edits that
  have been made to it.
* __@dependabot merge__ will merge this PR after your CI passes on it.
* __@dependabot squash__ and merge will squash and merge this PR after your CI
  passes on it.
* __@dependabot cancel__ merge will cancel a previously requested merge and
  block automerging.
* __@dependabot reopen__ will reopen this PR if it is closed.
* __@dependabot close__ will close this PR and stop Dependabot recreating it.
  You can achieve the same result by closing it manually.
* __@dependabot ignore this major version__ will close this PR and stop
  Dependabot creating any more for this major.
  version (unless you reopen the PR or upgrade to it yourself)
* __@dependabot ignore this minor version__ will close this PR and stop
  Dependabot creating any more for this minor version (unless you reopen the PR
  or upgrade to it yourself).
* __@dependabot ignore this dependency__ will close this PR and stop Dependabot
  creating any more for this dependency (unless you reopen the PR or upgrade to
  it yourself).

In the following link there are some options that can be configured in the
dependabot.yml file (e.g., package manager to use, how often to check for
updates, which dependencies to keep updated,...).

[configuration options for dependency aupdates](https://docs.github.com/en/github/administering-a-repository/configuration-options-for-dependency-updates)

Dependabot security alerts show up in the audit log if dismissed.

You can find the results in Insights tab in the submenu Dependency graph.

[Dependabot official documentation](https://docs.github.com/en/github/administering-a-repository/about-dependabot-version-updates)

#### Static application security testing (SAST)

We've tried to offer as many security gates as possible to all our global
developers and build teams. There is no one-size-fits-all, as I'm aware that
many commercial tools have numerous weaknesses. What we have tried to do is
offer a shopping cart experience, if you like, whereas the project manager or
scrum master responsible for the project, who understands the functional
specification of what is being developed can pick the right tool(s) for the job
at hand.

For example, the vision is to have a list of tools that are on offer and are
frequently reviewed by the Cyber Security team, to showcase the pros and cons
of said tools and ideal language support. This should foster a better
understanding of how the SAST/DAST functions work, including output, and how
this fits into the developer experience.

The journey our development teams take is really important, we want to move away
from old approaches that introduced security friction, such as tools that take
hours to scan code and therefore stop the pipeline from being agile to instant
feedback on bugs found during a git commit && git push and as such, there are
numerous smaller tools that make up this process.

With this in mind, we are looking to use a handful of both commercial and
opensource SAST tools, namely:

[Semgrep](https://semgrep.dev/): Is a fast open-source semantic code scanning
engine that fits into our pipelines and offers strict code checking options. It
also offers a community-driven rule repository that allows us to write rules for
unique codebases or bugs found by us, but also adopt checks for new
vulnerabilities faster than commercial scanners can.

![semgrep capture 1](../resources/semgrep.png)

Say, for example, we checked code in that made use of MD5, which is not ideal at
all. Semgrep caught this and rightly so

![semgrep capture 2](../resources/semgrep2.png)

I changed my code, did a git push and Semgrep checked that all was ok

![semgrep capture 3](../resources/semgrep3.png)

### CodeQL

[CodeQL](https://semmle.com/): Possibly the most exciting SAST engine to come
out in recent decades. Fast, accurate, and used extensively by Microsoft as part
of their Trustworthy Security engineering initiative, so much so they acquired
the tool and it is now part of Github Advanced Security.

One of the major benefits of LGTM is that they have a query language that lets
you query code as though it were data. This will allow us to write queries to
find all variants of a vulnerability, across all of our codebases.

![codeql screenshot](../resources/codeql.png)

The alerts can be classified/dismissed as "False Positive", "Used in tests" or
"Won't fix" to remove from the dashboard.

* Although LGTM allows for annotating the alert in the code (similar to SpotBugs
  annotations) this functionality has not yet been added to CodeQL in GitHub.
  The dismissed alerts don't show up in the audit log.
* Currently there is no way to create an issue from an alert in CodeQL, but this
  could be automated with a Workflow Action.

[CodeQL official documentation](https://securitylab.github.com/tools/codeql)

We also have more traditional SAST tools, such as Kiuwan and Sonar and Fortify
and they are used for more traditional codebases, which fits into the
shopping-cart comment I made above. What's really important is how these tools
fit into our pipelines and offer value right away.

## Docker vulnerability scanners

Ensuring our infrastructure is secure is as important as the code aspect and
this is where we are looking at how to scan and maintain our containers. From
ensuring the *docker pull* command pulls the latest official Santander image,
to ensuring running containers are scanned for runtime vulnerabilities.

* ### [Trivy-action](https://github.com/aquasecurity/trivy-action)

This is a GitHub action to scan for vulnerabilities in docker images, trivy
shows more vulnerabilities than Clair. Here an example to use it:

```yaml
- name: Run vulnerability scanner
  uses: aquasecurity/trivy-action@0.0.7
  with:
    image-ref: 'DOCKER_IMAGE:VERSION'
    format: 'table'
    exit-code: '1'
    ignore-unfixed: true
```

* ### [Clair](https://github.com/quay/clair)

Clair is an open source project for the static analysis of vulnerabilities in
application containers (currently including appc and docker).
[Clair scanner](https://github.com/arminc/clair-scanner.git) can be used to
analyze vulnerabilities in docker images, here an example to add it in a github
workflow:

```yaml
- name: Clair analysis
  env:
    GITHUB_USER: ${{env.GITHUB_ACTOR}}
    GITHUB_TOKEN: ${{secrets.GITHUB_TOKEN}}
  run: |
    # Clone the repo
    git clone https://github.com/arminc/clair-scanner.git
    cd clair-scanner/
    # Build and install clair-scanner
    make installLocal
    make build
    # run clair standalone agent and clair database
    docker run -p 5432:5432 -d --name db arminc/clair-db:latest
    docker run -p 6060:6060 \
           --link db:postgres -d \
           --name clair arminc/clair-local-scan:latest
    # get clair agent ip
    HOST_IP=$(ip -4 addr show docker0 | grep -Po 'inet \K[\d.]+')
    #run scanner
    ./clair-scanner -w example-alpine.yaml --ip $HOST_IP DOCKER_IMAGE:VERSION
```

The results appears in the action's output console, and it can be used to push
the docker image build before this step if the results are satisfactory or
discard it if fails.

Here the clair-scanner command information:

```shell script
    $ ./clair-scanner -h

    Usage: clair-scanner [OPTIONS] IMAGE

    Scan local Docker images for vulnerabilities with Clair

    Arguments:
    IMAGE=""     Name of the Docker image to scan

    Options:
    -w, --whitelist=""                    Path to the whitelist file
    -t, --threshold="Unknown"             CVE severity threshold. Valid values;
'Defcon1', 'Critical', 'High', 'Medium', 'Low', 'Negligible', 'Unknown'
    -c, --clair="http://127.0.0.1:6060"   Clair URL
    --ip="localhost"                      IP address where clair-scanner is
running on
    -l, --log=""                          Log to a file
    --all, --reportAll=true               Display all vulnerabilities, even if
they are approved
    -r, --report=""                       Report output file, as JSON
    --exit-when-no-features=false         Exit with status code 5 when no
features are found for a particular image
```

## Working with actions

In terms of a secure and efficient use of GitHub actions
it will be highly recommended to follow this **best practices**

### Workflows secrets

Notice that secrets are **not** shared to forked repositories and they can be **public**.

Access and manage of secrets works this way:

* For creating at **repo** level: Repository Admin access

* For creating at **org** level: Organization Owner to the org

Access policy for the secrets

* All repositories

* Private repositories

* Only selected repositories

Don't use structured data (like json): hard to redact

* Actions can do anything with secrets

* **Anyone with access to the Action Logs** have access to your secrets

* Secrets are **public**

### Workflows Runners

 A best practice is to run the action inside of a container

![imagen](https://user-images.githubusercontent.com/87127801/143555445-7e3ab99b-0878-43f2-a812-00bf0317e6c5.png)

![imagen](https://user-images.githubusercontent.com/87127801/143555728-3cdf17f1-1a3c-490a-b1ea-040df17e5a8b.png)

As well as **don't use self hosted runners for public repositories**

### Persisting data between runs

Don't share runners (and machines!) between repositories:

Risks:

* Malicious programs

* Escaping the runner sandbox

* Exposing access to the (network) environment

* Persisting unwanted or dangerous data

### Runners and security

We can found detailed information about this matter on this [security section](https://github.com/santander-group/santander-enterprise-account-public-documentation/blob/main/operational-model/docs/security.md#05-actions-from-trusted-sources)

### Protective measures

Manually:

* Check the action repo code before use

* Check its container images and dependencies before use

* Preferently use actions from verified creators

### Limiting actions altogether

Using a whitelist to manage actions use is highly recommended

![imagen](https://user-images.githubusercontent.com/87127801/143572278-03e13c7e-fee7-41eb-9fc3-d92b6b6d078e.png)

We can read more about security on actions runners on [this section](https://github.com/santander-group/santander-enterprise-account-public-documentation/blob/feature/security-in-action/operational-model/docs/security.md#05-actions-from-trusted-sources)

**Best practice** for using an action is to pin the action's commit SHA:

``` yaml
uses:gaurav-nelson/github-action-markdown-link-check@44a942b...
```

## Teams

Ensure role-based access controls are available and in use.

__Promote openness across your organization with an ‘all employees’ team__:
Repositories should be as open as possible to promote sharing, reuse, and
contribution across the organization. This way, all employees of your
organization can benefit from an open environment, while the access of
non-employee members of your GitHub organization can be more heavily restricted.
You can:

* Assign org default permission of at least _read_
* Creating an "all-employees" team and granting it appropriate access to all but
  the most top-secret repos.
* __Simplify team management with nested teams__: Most organizations use teams
  to manage their members’ access to repositories. Managing large numbers of
  teams is simplified when using nested teams.
* __Leverage internal expertise with ad-hoc teams__: Most businesses that use
  GitHub inherently map their team structure to their organization chart to
  provide appropriate levels of access. By itself this structure can leave blind
  spots as members of an existing team may not by themselves have the expertise
  necessary to solve certain problems.
  [Ad-hoc teams](https://github.blog/2017-02-15-organize-your-experts-with-ad-hoc-teams/)
  are supplemental to those teams used to manage permissions and are designed to
  let organization members self-organize around specific disciplines and topics.

## Repositories

* _Enforce and track repository settings_: [Probot](https://probot.github.io/)
  and [probot-settings](https://probot.github.io/apps/settings/) allows
  repository settings to be defined in a .yml file. If an administrator modifies
  the settings  to be something other than what is defined in the file,
  probot-settings will change them back. This provides enforcement of repository
  settings and empowers non-administrators to propose changes by simply opening
  up a pull request.

* __Topics__: Topics help organize your organization’s repositories. Topics are
  defined as any “word or phrase that roughly describes the purpose of a
  repository and the type of content it encapsulates“. Many open source
  communities and repositories use topics to categorize repositories by:

  * language/framework: Java - Spring-config, Node - React, Python - Django,
    Newman, Terratest, Pester, Selenium, ...
  * application type/product: pipeline-library-Jenkins, APIBundle-Apigee,
    plugin-Jenkins,Wordpress, Norkom, TIBCO, ...
  * project/maintaining team: team contact
  * target system: Azure, OHE, AWS, GitHub packages/Nexus (shared artifacts)
  * etc.

  From the main search bar simply scope your search to your organization by
  prefixing your search with "org:my-organization" to see topics and other
  results matching your query.

  * External users sharing policies

## Secret Scanning

* GitHub allows scanning our repositories (based on regular expressions or known
  secrets) and third-party repositories that publish our secrets.
* This would prevent commits of secrets (for example, a key from a database) in
  our repositories.
* Scanning third-party repositories would allow us to search for our secrets if
  there has been a leak.

__References__:
[About secret scanning official documentation](https://docs.github.com/en/code-security/secret-security/about-secret-scanning)

## Secrets

Hightlights:

* Can be managed via [API](https://developer.github.com/v3/actions/secrets/)
* You can control which repositories can use secrets at the Organization level
  via access policies
* To create secrets for a repository of an Organization, administrator access is
  required
* When creating a secret in an organization, a policy can be used to limit the
  access of repositories to this secret.
* A repository can access secrets of the parent organization (if the
  corresponding permissions are configured)
* To make a secret available for an Action, you must configure the secret as an
  input or environment variable in the workflow
* Secrets (except GITHUB_TOKEN) are not passed to the worker when a workflow is
  fired from a repository fork. This ensures that no secrets are used outside of
  the original repository.
* In Actions, setting a secret in an environment variable must be done by using
  the “secrets” context.
* Passing secrets between processes from the command line is not recommended, as
  they may be visible to other users or be captured by audit events. On the
  contrary, the recommendation is environment variables, STDIN or other
  mechanisms supported by the target process.
* You can launch projects from GitHub to a private server via SSH key adding the
  public key to the repository and the private part in the server. In this case,
  keys with write access can perform the same actions as a member of the
  organization with administrator access or a collaborator in a personal
  repository.

__Advantages__:

* Anyone with access to the repository and the server can deploy the project.
* Users do not have to change their local SSH settings.

__Disadvantages__:

* Keys only grant access to a single repository.
* They are generally not protected by a phrase, so it can be easily accessible
  if the server looksvcommitted.
* Anyone with access to the repository can extract these secrets through a
  pipeline.

To mitigate the previous last point, you can clone the repository, so the
secrets are only in the repository cloned and considered as a "deployment"
repository. This deployment repository has more restricted access.  Recently
GitHub has released a new functionality called
[environments](https://docs.github.com/en/actions/reference/environments).
You could use it for better custody of secrets by restricting access.

__References__:

* [Creating and storing encrypted secrets](https://docs.github.com/en/actions/reference/encrypted-secrets).
* [Secrets](https://developer.github.com/v3/actions/secrets/)

## Deploy keys

* Only organization owners or users with admin rights in a repo (via Teams or
  directly) can add deployment keys to a repo.
* The "deployment keys" are read-only by default but can be given write access
  by adding them to a repository.
* In principle, the use of deployment keys is only to pull / push to the
  repositories by SSH protocol, if you want to interact with the GitHub API,
  you would have to create an application
  [GitHub Apps](https://developer.github.com/apps/about-apps/)
* In case of using a deploy key for Continuous Deployment, the software
  deployment would be done outside of GitHub, so there would be no secret on
  GitHub. Only the public key would be in the repository, so the Repository
  contributors could not extract any secrets.
* Disadvantage: once a deployment key has been created, it could be used by any
  application or user without being associated with a specific GitHub user. So
  it is crucial to protect the private part of the key well.

## Using secure actions

This section details the steps to evaluate public catalog github actions
following best practices.

GitHub Actions are potent tools, and they rely on the GitHub open-source
community to develop them. The community is in charge of security too, and they
rely on the tools previously mentioned in this document. But, the main problem
with security is not the mistakes made during the development process. It is the
attempt to include malicious code into a legitimate GitHub Action. To prevent
using doubtful actions origin or with bad intentions, we plan an easy way to
provide verified actions, safe to use.

We will use
[self-hosted runners](https://docs.github.com/en/actions/hosting-your-own-runners)
to execute some GitHub Actions, it gives us connection to internal tools like
nexus, sonar, kiwan, harbor... So it is important to validate actions before
execute it in a workflow that use shelf hosted runners.

### First security layer

Only use actions from trusted providers:

* GitHub Actions provided by GitHub itself.
* Actions from verified providers.
* Actions from Santander Group Development Community.

To accomplish the first security layer, there are some settings you can tune in
your organisation like the following:

![security organization actions configuration](../resources/security-org-action-configuration.png)

As you can see in the screenshot, you can enable
`Allow actions created by GitHub` and
`Allow Marketpalce actions by verified creators` checkboxes. These two options
will prevent using unknown creators' actions.

But not all not verified creators are evil. In that case, you can add that
action to the whitelist in the textbox, but be sure to specify the exact commit
of that action to prevent a version swapping to inject harmful code in the
action.

### Second security layer

  1. [General overview](#general-overwiew)
  2. [Actions security](#actions-security)
  3. [Toolkit proposal](#toolkit-proposal)
  4. [Automate the process](#automate-the-process)

---

### General overview

We detailed the steps to evaluate public catalog GitHub actions
following best practices.

It is important to validate actions before execute it in a workflow that use
shelf hosted runners.

We will use [self-hosted runners](https://docs.github.com/en/actions/hosting-your-own-runners)
to execute GitHub actions, it gives us connection to internal tools like
nexus, sonar, kiwan, harbor...

#### Actions security

To test the action we can fork the action repository and activate all
security and analysis features as is describe in this documentation
[managing-security-and-analysis-settings](https://docs.github.com/en/organizations/keeping-your-organization-secure/managing-security-and-analysis-settings-for-your-organization)

When repository security is configure dependabot alerts are enabled,
then we can display dependency issues for the action under
'Security' > 'Dependabot alerts'.

Dependaboot create a pull request with the issues that detect in the scann.
xº
In this section we can evaluate the issues and determine whether the
action is secure or not depending on the criticality of the vulnerabilities.

### Toolkit proposal

We can also create a workflow to analyse the source code, GitHub proposed some
actions to do this job, you find them in 'Security' > 'Code scanning alerts' >
'Setup more code scanning alerts'. It is important to test the commit
hash to ensure the action version are going to use in the project.

* Automatically detect common vulnerabilities and coding errors
  * **[CodeQL Analysis](https://codeql.github.com/docs/codeql-overview/about-codeql/):**
  Security analysis from GitHub for C, C++, C#, Java,
  JavaScript, TypeScript, Python, and Go developers.

* Security analysis from the Marketplace:
  * **[Codacy Security Scan](https://github.com/marketplace/actions/codacy-analysis-cli):**
   Free, out-of-the-box, security analysis provided by multiple
   open source static analysis tools.
   [integration-with-github-code-scanning](https://github.com/marketplace/actions/codacy-analysis-cli#integration-with-github-code-scanning)
  * **[Code Scan](https://github.com/marketplace/actions/codescan-scanner):**
   CodeScan allows for better visibility on your code
   quality checks based on your custom rulesets.
  * **[Fortify on Demand Scan](https://github.com/marketplace/actions/fortify-on-demand-scan)**
   Integrate Fortify's comprehensive static code analysis(SAST) for 27+
   languages into your DevSecOps workflows to build secure software faster.

#### Automate the process

Automate the process to evaluate GitHub Actions with a workflow.

With [this script](https://github.com/santander-group/action-security-generator-script)
we are able to replicate public GitHub Actions
in a personal repository, it creates a dedicated workflow
script that automatically search for source code and dependencies
vulnerabilities.

To use this script you have to create a new repository in your GitHub
organization, it is important to name the repo with the same name as
the action repository name.

Then follow the instructions to
[configure dependabot](https://docs.github.com/en/code-security/supply-chain-security/managing-vulnerabilities-in-your-projects-dependencies/configuring-dependabot-security-updates)
in the new repository.

#### Discutions link

<https://github.com/santander-group/product-office>

#### References

* [About alerts for vulnerable dependencies](https://docs.github.com/en/code-security/supply-chain-security/about-alerts-for-vulnerable-dependencies)
* [Managing security and analysis settings for your user account](https://docs.github.com/en/github/setting-up-and-managing-your-github-user-account/managing-security-and-analysis-settings-for-your-user-account)
* [Helping maintainers combat bad actors](https://github.blog/2021-04-22-github-actions-update-helping-maintainers-combat-bad-actors)
* [Configure dependabot security updates](https://docs.github.com/en/code-security/supply-chain-security/configuring-dependabot-security-updates)
* [GitHub REST Api endpoints](https://docs.github.com/en/rest/overview/endpoints-available-for-github-apps#actions)
* [Configuring code scanning](https://docs.github.com/en/code-security/secure-coding/automatically-scanning-your-code-for-vulnerabilities-and-errors/configuring-code-scanning)

[go back](../README.md)
