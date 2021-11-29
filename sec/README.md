# Security

## Table Of Content

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

* [Organization Owner Security Decalogue (The Octo-logue)](#organization-owner-security-decalogue-the-octo-logue)
  * [01 Control what is exposed to the Internet](#01-control-what-is-exposed-to-the-internet)
    * [Repository Visibility](#repository-visibility)
    * [Data exposed through your organization members profiles](#data-exposed-through-your-organization-members-profiles)
  * [02 Keep your organization clean and tidy](#02-keep-your-organization-clean-and-tidy)
  * [03 Know who is accessing your repositories](#03-know-who-is-accessing-your-repositories)
  * [04 Do not allow External Collaborators accounts on your organization](#04-do-not-allow-external-collaborators-accounts-on-your-organization)
  * [05 Actions from trusted sources](#05-actions-from-trusted-sources)
  * [06 GitHub Apps running in secure environments](#06-github-apps-running-in-secure-environments)
  * [07 Follow the Principle of least privilege](#07-follow-the-principle-of-least-privilege)
  * [08 Controlling and Monitoring your organization billing](#08-controlling-and-monitoring-your-organization-billing)
* [Safeguard content with encryption-at-rest](#safeguard-content-with-encryption-at-rest)
* [Encryption-in-transit is used for all communication between endpoints](#encryption-in-transit-is-used-for-all-communication-between-endpoints)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Organization Owner Security Decalogue (The Octo-logue)

As much as we create guiderails to help keep you secure, there's an element of
you also helping with regards to security.

The organization owner (OO), the ultimate owner and one with the biggest
responsibility for the group, has to ensure we are all compliant and following
the rules. In addition to this role, the ALM Multicloud Operations Team
(ALMMCOT) also help and as such require administrative roles in the
organization.

Being the owner of an organization under the Santander Group GitHub Enterprise
Account implies a big responsibility.
We follow [Peter Parker's Principle](https://en.wikipedia.org/wiki/With_great_power_comes_great_responsibility)
(PPP):

     With great power, there must also come great responsibility

The OO access role is similiar to God-mode in the overall setup. There are
plenty of settings that can be modified by an OO, some aren't harmful, and
others are very harmful. However, as the organizations belong to an Enterprise
Account, we have many other options that cannot be changed. So, when the OOs
review their organization settings, they could find some configuration options
that cannot be changed despite the God-like mode. I.e., the next picture
represents the connection to the IdP that is established to the Santander Group
Azure Active Directory and can't be changed:

![saml connection disabled at organization level](../resources/security-samlconnectiondisable.png)

It can't be changed because that configuration is set at the Enterprise Account
level. So, don't go mad.

As a God-like user, the OO can access all repositories in the organization. It
doesn't matter the repo visibility or the repo content, the OO is the only one
that is able to change some repository settings. I.E., the project visibility
change setting is configured at Enterprise Account level; then the organization
inherit the configuration and that configuration applies to the repository. Even
the repository admin can't change repository visibility, needs the OO approval:

![visibility change disable at repository configuration level](../resources/security-visibilitychangedisabled.png)

The OO can take a look at all organization settings options looking for all the
read-only parameters to know which ones are under Enterprise Account control.
Those options can be identified by this message:

![message about property controlled by the enterprise account](../resources/security-optioncontroledbyea.png)

Or the OO can visit our [setup model section](../../setup-model/README.md).

### 01 Control what is exposed to the Internet

GitHub.com is located on the Internet, so everybody around the world has access
to it. Considering the data stored in the Santander Group GitHub Enterprise
Cloud Account as __Confidential-Restricted__, OO needs to be careful about what
is exposed. It is not only about the repository content; it is also about the
user profiles (public in GitHub), the organization description, name, avatar,
etc. Please be gentle and careful, and think that losing reputation is
also a risk. It is vital that the OO ensures all organization members are aware
of this and act accordingly.

#### Repository Visibility

By default, organization members can create repositories inside their
organizations. The only limitation being the visibility of that repositories.

__By default, all new repositories are either internal or private.__

In order to control public visibility of code and content, only the OO user can
make a repository public and we ask that this is only performed when all the
necessary checks (security, license, code quality etc) have been performed and
validated.

It is important to stress that the OO is not left alone to handle this but has
the ALM Multicloud Operations Team to help. We are constantly scanning all
public repositories and will act to secure them if we do find any that haven't
been assessed.

What if you want a public repository?

We aren't stopping the sharing of code and collaration, so if you wish to make
your repo public, you need a  __CISO waiver__ or an __Operational Risk
Assessment__ (ORA). The OO can talk to the CISO, and the CISO can add the public
repo to the Public Repo Whitelist. In that case, the operations team will take
no preventive measures.

#### Data exposed through your organization members profiles

It's important that we all play a role here to ensure all data, both yours and
the organization is secure. For all users, the OO should make users aware of the
[users profiles best practice advice](../../best-practices/docs/identity.md#users).

### 02 Keep your organization clean and tidy

It's important that we all help with administrative tasks to maintain order.
This helps detect anomalies so we can act faster to prevent incidents. To help
with this, we are using as many GitHub features on offer, for example:

1. Topics -
  [Topics](https://docs.github.com/en/github/administering-a-repository/classifying-your-repository-with-topics)
  are labels that create subject-based connections between GitHub repositories
  and let you explore projects by type, technology, and more. We've written some
  ideas about this over in the
  [topics section](../../best-practices/docs/basic.md#topics).

2. Another way to keep your organization under control is establishing a
  repository naming agreement with the organization members. That members could
  be organized in
  [GitHub Teams,](https://docs.github.com/en/github/setting-up-and-managing-organizations-and-teams/about-teams)
  which will offer huge organizational possibilities. Check out some ideas over
  [here](../../setup-model/docs/basic.md#Organizationteamsstructure)

3. Monitoring - knowing what you have is really
  [vital](../../best-practices/docs/basic.md#metrics-available).

### 03 Know who is accessing your repositories

Knowing who has access to what in any organization is a must for security
reasons. We have hundreds of repos and users so maintaining an understanding of
who what and where is a role that the ALMMCOT will assist in performing. This
will be handled by periodical reminders about a procedure called '_user
re-certification_'. This process consists of listing all organization members
and checking their access level or privileges. This list has to be reviewed on a
regular basis by the OO.

Thankfully we can automate this using the API, like [so](https://docs.github.com/en/graphql/overview/explorer):

    {
      organization(login: "THE-ORGANIZATION-NAME") {
        id
        name
        repositories(first: 100, orderBy: {field: NAME, direction: ASC}) {
          pageInfo {
            endCursor
            hasNextPage
          }
          edges {
            node {
              name
              collaborators(first: 100) {
                edges {
                  node {
                    name
                    login
                  }
                  permission
                }
              }
            }
          }
        }
      }
    }

__Note__: The OO needs to grant GraphQL Explorer access to organization in the
 _Organization Settings_, _Third Party Access_ tab:

![security grant access](../resources/security-grantaccesstographql.png)

### 04 Do not allow External Collaborators accounts on your organization

We all love extra help and external collaborator accounts can often help us when
we are stuck, but they are forbidden under the Santander Group GitHub Cloud
Enterprise Account.

The reason is straightforward: the external collaborator accounts
don't use the SAML connection configured in the organization, which implies that
the official Identity Provider (Azure Active Directory) does not provide the
authentication, identification and authorization. That allows every GitHub user
to be added as a member in a repository and access the information stored.

There is a need to control who has access to what.

However, we are able to invite external collaborators to participate by having
the OO send the invitation once checks have been performed. The ALMMCOT helps
this process by vetting and controlling such access. For more information on
this
[visit this guide.](../../setup-model/docs/security.md#outside-collaborators)

There are some requirements to take into account when adding external
collaborators, such as:

1. The security of that users account. Do they have 2FA/MFA enabled? Are they
  sing GPG etc?
2. Complete logging of who is accessing what. This will be required by the
  ALMMCOT team to track access.
3. Externals should only be added at a repository level and not an
  organizational one.
4. External collaborators should adopt the same security we do and we should
  validate this.
5. Once the above has been validated, they should be added to the  _External
  Users Whitelist_.

### 05 Actions from trusted sources

GitHub Actions are very cool but there exists a possibility whereby untrusted
actions could execute undesired commands. As with all things on the Internet,
read the code you want to use to ensure the actions do what you expect them to
do. A good idea is to
[allow only specific actions to run.](https://docs.github.com/en/github/setting-up-and-managing-organizations-and-teams/disabling-or-limiting-github-actions-for-your-organization#allowing-specific-actions-to-run)
Action usage will be monitored to ensure we are all keeping safe.

Protective measure are detailed at [this section](https://github.com/santander-group/santander-enterprise-account-public-documentation/blob/feature/security-in-action/best-practices/docs/security.md#protective-measures)

And we can find about limiting actions on this [another section](https://github.com/santander-group/santander-enterprise-account-public-documentation/blob/feature/security-in-action/best-practices/docs/security.md#limiting-actions-altogether)

### 06 GitHub Apps running in secure environments

[GitHub Apps](https://docs.github.com/en/developers/apps/about-apps) are great,
and can assist in performing repetitive tasks. When a GitHub App is added to an
organization, it will ask for permissions. The OO is responsible for ensuring
that the permissions being requested and the app(s) are legitimate and needed.
Questions that should be asked are:

1. Where are the apps running?
2. What level of permissions are being asked for? is this normal?
3. Are the apps executing outside of the groups perimeter?

### 07 Follow the Principle of least privilege

At all times should privileges be used and given for the task at hand.
Repository admins should apply this principle too.

### 08 Controlling and Monitoring your organization billing

Finally, the OO can check, the usage of the GitHub resources by accessing the
Billing & Plans tab in the organization settings section:

![billing tab](../resources/security-controlbilling.png)

In this section, the OO can check for anomalies in the consumption, so it will
give an idea of what is happening

## Safeguard content with encryption-at-rest

* User profile data: GitHub takes all measures reasonably necessary to protect
  User Personal Information from unauthorized access, alteration, or
  destruction; maintain data accuracy; and help ensure the appropriate use of
  User Personal Information. See
  [how GitHub secures your information](https://docs.github.com/en/github/site-policy/github-privacy-statement#how-github-secures-your-information)
  for more info.
* Project data: Git repository content is encrypted at rest.
  See [how GitHub secures your information](https://docs.github.com/en/github/site-policy/github-privacy-statement#how-github-secures-your-information)
  for more info.
* [Secrets](../../best-practices/docs/basic.md#secrets)
* Agents data

## Encryption-in-transit is used for all communication between endpoints

* [Enforcing HTTPS for your Github Pages site](https://docs.github.com/en/github/working-with-github-pages/securing-your-github-pages-site-with-https#enforcing-https-for-your-github-pages-site)

[go back](../README.md)
