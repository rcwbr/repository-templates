# repository-templates<a name="repository-templates"></a>

[![GitHub Release](https://img.shields.io/github/v/release/rcwbr/repository-templates?logo=semver&style=flat-square)](https://github.com/rcwbr/repository-templates/releases/latest)
[![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/rcwbr/repository-templates/push-workflow.yaml?logo=github&style=flat-square)](https://github.com/rcwbr/repository-templates/actions/workflows/push-workflow.yaml?query=branch%3Amain)
[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit&logoColor=white&style=flat-square)](https://github.com/pre-commit/pre-commit)
[![Commitlint](https://img.shields.io/badge/commitlint-enabled-navy?style=flat-square&logo=commitlint)](https://github.com/conventional-changelog/commitlint)
[![Conventional Commits](https://img.shields.io/badge/conventional_commits-compliant-pink?style=flat-square&logo=conventionalcommits&logoColor=white)](https://www.conventionalcommits.org)

Templates for new GitHub repositories with environments, settings, and automation.

<!-- mdformat-toc start --slug=github --maxlevel=6 --minlevel=1 -->

- [repository-templates](#repository-templates)
  - [Overview](#overview)
  - [Usage](#usage)
    - [Creating a new repository](#creating-a-new-repository)
      - [Step 1. Create repository](#step-1-create-repository)
      - [Step 2. Create GitHub authentication and configure Apps](#step-2-create-github-authentication-and-configure-apps)
      - [Step 3. Prepare type-specific resources](#step-3-prepare-type-specific-resources)
        - [Python template](#python-template)
      - [Step 4. Copy and configure template](#step-4-copy-and-configure-template)
      - [Step 5. Commit repo configuration](#step-5-commit-repo-configuration)
  - [Contributing](#contributing)
    - [devcontainer](#devcontainer)
      - [devcontainer basic usage](#devcontainer-basic-usage)
      - [devcontainer Codespaces usage](#devcontainer-codespaces-usage)
      - [devcontainer pre-commit usage](#devcontainer-pre-commit-usage)
    - [CI/CD](#cicd)
    - [Settings](#settings)

<!-- mdformat-toc end -->

## Overview<a name="overview"></a>

A collection of GitHub repository templates providing standardized development environments, CI/CD
workflows, and documentation. Templates are sourced from proven patterns used in production
repositories.

## Usage<a name="usage"></a>

### Creating a new repository<a name="creating-a-new-repository"></a>

#### Step 1. Create repository<a name="step-1-create-repository"></a>

Via the GitHub UI, create a repository and initialize with a README.md file.

In repository settings, navigate to Pages and select GitHub Actions as the Source under Build and
deployment.

In repository settings, configure a Codespaces prebuild:

1. Select Set up prebuild
1. Select the default branch
1. Set Prebuild triggers to Every push
1. Select appropriate region availability to your case
1. Set Template history to 1
1. Optionally, specify recipients for Failure notifications

#### Step 2. Create GitHub authentication and configure Apps<a name="step-2-create-github-authentication-and-configure-apps"></a>

Install the [Probot settings GitHub App](https://probot.github.io/apps/settings/) by selecting Add
to GitHub then selecting the new repository under Repository access.

Create a GitHub App to enable automated releases. Follow
[this link](https://github.com/settings/apps/new?name=NEW_PROJECT%20CI%20release-it&url=https://github.com/NEW_ORG/NEW_PROJECT&description=This%20app%20enables%20the%20NEW_PROJECT%20GitHub%20Actions%20workflow%20to%20authenticate%20as%20an%20app%20to%20publish%20tags%20and%20releases&webhook_active=false&contents=write)
to create an App, or follow these manual steps:

1. Navigate to [GitHub Settings > Developer Settings](https://github.com/settings/apps)
1. Create a New GitHub App
1. Under Webhook, disable Active
1. Under Permissions, set Contents to Read and Write

After App creation, configure it for use in the new repo:

1. Generate a private key and save the App ID for later
1. Install the App to your organization, selecting only the new repository
1. Create an environment named `Repo release` in the repository Settings
1. Add the App private key as a secret named `RELEASE_IT_GITHUB_APP_KEY` in that environment

Create a
[Personal Access Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic)
with `write:packages` scope.

1. In repository settings for the new repo,
   [add a Codespace secret](https://docs.github.com/en/codespaces/managing-codespaces-for-your-organization/managing-development-environment-secrets-for-your-repository-or-organization#adding-secrets-for-a-repository)
   named `CODESPACES_PREBUILD_TOKEN` with the token as the value
1. Add a secret named `USER` with value `codespace`
1. Add a secret named `UID` with value `1000`
1. Keep the token handy for step 4 of this guide

#### Step 3. Prepare type-specific resources<a name="step-3-prepare-type-specific-resources"></a>

Set up resources for any of the following specific types that apply to the new repo.

##### Python template<a name="python-template"></a>

Create a GitHub App to enable automated writes to an mkdocs branch. Follow
[this link](https://github.com/settings/apps/new?name=NEW_PROJECT%20CI%20mkdocs&url=https://github.com/NEW_ORG/NEW_PROJECT&description=This%20app%20enables%20the%20NEW_PROJECT%20GitHub%20Actions%20workflow%20to%20authenticate%20as%20an%20app%20to%20publish%20to%20the%20docs%20branch&webhook_active=false&contents=write)
to create an App, or follow these manual steps:

1. Navigate to [GitHub Settings > Developer Settings](https://github.com/settings/apps)
1. Create a New GitHub App
1. Under Webhook, disable Active
1. Under Permissions, set Contents to Read and Write

After App creation, configure it for use in the new repo:

1. Generate a private key and save the App ID for later
1. Install the App to your organization, selecting only the new repository
1. In the repository Settings, navigate to environment named `github-pages` created during step 1
1. Add the App private key as a secret named `DOCS_GITHUB_APP_KEY` in that environment

Export the App ID:

```bash
export NEW_MKDOCS_APP_ID=<newly created app ID>
```

Prepare PyPi for publishing:

1. Visit [PyPi publishing settings](https://pypi.org/manage/account/publishing/)
1. Under Add a new pending publisher, select the GitHub tab
1. Set PyPI Project Name to the name of the package (matching `pyproject.toml`)
1. Set Owner to the GitHub org/user
1. Set Repository name to the new repository name
1. Set Workflow name to `push-workflow.yaml`
1. Set Environment name to `pypi`

Install the [Codecov GitHub App](https://github.com/apps/codecov) and configure it to apply to the
new repo. Follow the prompt to log into GitHub, and configure the new repo on the Codecov landing
page as well. Select Using GitHub Actions, then scroll to step 3. Copy the value provided for the
`CODECOV_TOKEN` shown there.

Navigate to the new repo settings, and add an Actions Repository secret named `CODECOV_TOKEN`, with
the value copied.

#### Step 4. Copy and configure template<a name="step-4-copy-and-configure-template"></a>

Prepare environment variables with the names of the org, project, local path, and release app ID
(from step 2):

```bash
export NEW_ORG=<my-org>
export NEW_PROJECT=<my-project-name>
export NEW_REPO_PATH=</path/to/my-project>
export NEW_RELEASE_IT_APP_ID=<app-id-from-step-2>
export SECRET_PREFIX=$(echo "$NEW_PROJECT" | tr '[:lower:]' '[:upper:]' | tr '-' '_')
```

Clone the new repository and apply the base to it:

```bash
git clone "https://github.com/${NEW_ORG}/${NEW_PROJECT}.git" "${NEW_REPO_PATH}"

# 1. Apply base template
./templates/base/apply-template
```

Follow the instructions printed by the script to configure Codespaces user secrets for the PAT from
step 2.

If applicable, apply the type-specific templates to the new repo:

```bash
# 2. Add type-specific templates (optional)
./templates/docker/apply-template    # For Docker projects
# or
./templates/python/apply-template    # For Python projects
```

Execute the above `apply-template` call for each type applicable to the new repo.

#### Step 5. Commit repo configuration<a name="step-5-commit-repo-configuration"></a>

Commit and push the new repository configuration:

```bash
cd "$NEW_REPO_PATH"
git add .
git commit -m "feat: initial project setup"
git push -u origin main
```

## Contributing<a name="contributing"></a>

### devcontainer<a name="devcontainer"></a>

This repo contains a [devcontainer definition](https://containers.dev/) in the `.devcontainer`
folder. It leverages the
[devcontainer cache build tool](https://github.com/rcwbr/devcontainer-cache-build) and
[layers defined in the dockerfile-partials repo](https://github.com/rcwbr/dockerfile-partials).

#### devcontainer basic usage<a name="devcontainer-basic-usage"></a>

The [devcontainer cache build tool](https://github.com/rcwbr/devcontainer-cache-build) requires
authentication to the GitHub package registry, as a token stored as
`REPOSITORY_TEMPLATES_DEVCONTAINER_INITIALIZE` (see
[instructions](https://github.com/rcwbr/devcontainer-cache-build/tree/main?tab=readme-ov-file#initialize-script-github-container-registry-setup)).

#### devcontainer Codespaces usage<a name="devcontainer-codespaces-usage"></a>

For use with Codespaces, the `REPOSITORY_TEMPLATES_DEVCONTAINER_INITIALIZE` token (see
[devcontainer basic usage](#devcontainer-basic-usage)) must be stored as a Codespaces secret (see
[instructions](https://github.com/rcwbr/devcontainer-cache-build/tree/main?tab=readme-ov-file#initialize-script-github-container-registry-setup)),
as must values for `USER`, and `UID` (see [useradd Codespaces usage](#useradd-codespaces-usage)).

#### devcontainer pre-commit usage<a name="devcontainer-pre-commit-usage"></a>

By default, the devcontainer configures [pre-commit](https://pre-commit.com/) hooks in the
repository to ensure commits pass basic testing. This includes enforcing
[conventional commit messages](https://www.conventionalcommits.org/en/v1.0.0/) as the standard for
this repository, via [commitlint](https://github.com/conventional-changelog/commitlint).

### CI/CD<a name="cicd"></a>

This repo uses the [release-it-gh-workflow](https://github.com/rcwbr/release-it-gh-workflow), with
the conventional-changelog image defined at any given ref, as its automation. It leverages the
[devcontainer-cache-build workflow](https://github.com/rcwbr/devcontainer-cache-build/blob/main/.github/workflows/devcontainer-cache-build.yaml)
to pre-generate devcontainer images, which are also used for the
[pre-commit workflow](https://github.com/rcwbr/dockerfile-partials/blob/main/.github/workflows/pre-commit.yaml).

### Settings<a name="settings"></a>

The GitHub repo settings for this repo are defined as code using the
[Probot settings GitHub App](https://probot.github.io/apps/settings/). Settings values are defined
in the `.github/settings.yml` file. Enabling automation of settings via this file requires
installing the app.

The settings applied are as recommended in the
[release-it-gh-workflow usage](https://github.com/rcwbr/release-it-gh-workflow/blob/4dea4eaf328b60f92dab1b5bd2a63daefa85404b/README.md?plain=1#L58),
including tag and branch protections, GitHub App and environment authentication, and required
checks.
