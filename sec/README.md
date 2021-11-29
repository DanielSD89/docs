# Security

## Table Of Content

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

* [Santander Group GitHub Cloud Enterprise Account security level policies](#santander-group-github-cloud-enterprise-account-security-level-policies)
  * [Repository visibility](#repository-visibility)
  * [Outside Collaborators](#outside-collaborators)
  * [Actions](#actions)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Santander Group GitHub Cloud Enterprise Account security level policies

**Policies at Enterprise Account level should be disabled by default. Only
listed below need to be set.**

### Repository visibility

Most organizations have explicit policies and procedures for determining which
repositories can be contributed to the open-source community. On GitHub.com any
repository that is ‘public’ is available for the world to see and use. GitHub
provides organization administrators with the ability to prohibit organization
members from changing their repository’s visibility.

The default repository visibility can be: **private** or **internal**. You
shouldn't create a public repository from scratch. With this enabled, only
organization owners will be able to change a repository’s visibility. The
properties set at Enterprise Account level to perform that are:

* Policies -> Repositories -> Repository creation -> Members can create
  repositories: Public (unchecked), Internal (checked), Private (checked).

  ![members-can-create-repositories-policy](../resources/security-members-can-create-repositories-policy.png)

* Policies -> Repositories -> Repository visibility change -> Disabled.

  ![repository-visibility-change-policy](../resources/security-repository-visibility-change-policy.png)

* Policies -> Repositories -> Repository deletion and transfer -> Disabled.

  ![respository-deletion-and-transfer-policy](../resources/security-respository-deletion-and-transfer-policy.png)

* Policies -> Repositories -> Repository issue deletion -> Disabled.

  ![repository-issue-deletion-policy](../resources/security-repository-issue-deletion-policy.png)

* Policies -> Repositories -> Repositories forking -> Disabled

  ![repositories-forking-policy](../resources/security-repositories-forking-policy.png)

  To avoid the possible data leaking outside Santander's Enterprise Account, the
  forking should be disabled to prevent a repository to be cloned outside the
  Enterprise Account.

### Outside Collaborators

They can be added to individual projects and cannot be added at the organization
level. Every outside collaborator consumes a license when accepting the
invitation and releasing it when deleted from the project. To keep the
collaborators' number as low as possible, only organization owners should be
able to invite them. So this policy at Enterprise Account needs to be set to
disable:

* Policies -> Repositories -> Repository invitations -> Disabled.

  ![repository-invitations](../resources/security-repository-invitations.png)

External collaborators are strictly forbidden. They must be removed from their
organization and repositories.

[Remove outside collaborators GitHub Apps](https://github.com/apps/remove-outside-collaborators)
can be configured to automatically remove all outside collaborators as soon as
they are added. It can also be configured to only allow those outside
collaborators specifically defined in a whitelist. Be careful with the GitHub
Apps you install in the organization. Check out our recommendations at the
[Octo-Logue](../../operational-model/docs/security.md#06-GitHub-Apps-running-in-secure-environments)
about the GitHub Apps execution.

### Actions

* Policies -> Actions -> Enable for all organizations -> Allow Selectd Actions
  (selected) -> Allow Actions Created by GitHub (checked) and Allow Marketplace
  actions by verified creators (checked)

![actions-allowed](../resources/security-actions-allowed.png)

[go back](../README.md)
