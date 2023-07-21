# Github Actions Templates

This project contains all Github Actions templates. To make use of the repository, fork this repository and modify the `env` and `trigger` sections of each template to meet the needs of your application or repository.

- [Workflow Templates](#workflow-templates)
  - [Build Push Docker](#docker-build-push)
  - [Trivy Scan](#trivy-scan)
  - [SonarCloud Scan](#sonar-repo-scan)
  - [Husky Scan](#husky-scan)
  - [Gitleaks Scan](#gitleaks-scan)
  - [OWASP ZAP Scan](#owasp-zap-scan)
  - [Playwright Tests](#playwright-tests)
- [Workflow Triggers](#workflow-triggers)
- [Secrets Management](#secrets-management)
- [Specifying Action Versions and Branches](#specifying-action-versions-and-branches)
- [Keeping Your Actions Up-to-Date with Dependabot](#keeping-your-actions-up-to-date-with-dependabot)
- [Reference](#reference)

## How to Use

1. To get started, copy one of the [workflow templates](#workflow-templates) to your own repository under `.github/workflows/<workflow_name>`.

2. Create any referenced secrets in **settings > repository secrets**.

3. Push a change to trigger the workflow. The workflow will call the `button-inc/button-shared-gh-actions` repository.

When a workflow is called, it is imported into the callers context, and executes as if all the logic is running locally within the repository.

## Workflow Templates

### Docker Build Push

```yaml
name: build-push-docker
on:
  workflow_dispatch:
  push:
jobs:
  build-push:
    uses: button-inc/button-shared-gh-actions/.github/workflows/build-push-docker.yml@develop
    with:
      image-registry: docker.io
      image: gregnrobinson/bcgov-nginx-demo
      workdir: ./demo/nginx
    secrets:
      image-registry-user: ${{ secrets.image-registry-user }}
      image-registry-password: ${{ secrets.image-registry-password }}
```

[Back to top](#github-actions-templates)

### Trivy Scan

```yaml
name: trivy-scan
on:
  workflow_dispatch:
  push:
jobs:
  trivy-scan:
    uses: button-inc/button-shared-gh-actions/.github/workflows/scan-code-trivy.yml@develop
```

[Back to top](#github-actions-templates)

### SonarCloud Scan

```yaml
name: sonarcloud-scan
on:
  workflow_dispatch:
  push:
jobs:
  sonarcloud-scan:
    uses: button-inc/button-shared-gh-actions/.github/workflows/scan-code-sonarcloud.yml@develop
    secrets:
      github-token: ${{ secrets.GITHUB_TOKEN }}
      sonar-token: ${{ secrets.SONAR_TOKEN}}
```

[Back to top](#github-actions-templates)

### Husky Scan

```yaml
name: husky-scan
on:
  workflow_dispatch:
  push:
jobs:   
  husky-scan:
    uses: button-inc/button-shared-gh-actions/.github/workflows/scan-code-husky.yml@develop
    with:
      working-directory: ./app
      node-version: '18' 
```

[Back to top](#github-actions-templates)

### Gitleaks Scan
`gitleaks.toml` in the root dir allows you write your own secret detection rules, [here](https://github.com/gitleaks/gitleaks#configuration) for more information.
```yaml
name: gitleaks-scan
on:
  workflow_dispatch:
  push:
jobs:   
  gitleaks-scan:
    uses: button-inc/button-shared-gh-actions/.github/workflows/scan-code-gitleaks.yml@develop
  secrets:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    gitleaks-license: ${{ secrets.GITLEAKS_LICENSE}}
```

[Back to top](#github-actions-templates)

### OWASP ZAP Scan
It builds the app and allows ZAP scans against localhost.
```yaml
name: zap-scan
on:
  workflow_dispatch:
  push:
jobs:   
  zap-scan:
    uses: button-inc/button-shared-gh-actions/.github/workflows/scan-code-owasp-zap.yml@develop
    with:
      target-url: 'http://localhost:3000'
```

[Back to top](#github-actions-templates)
### Playwright Tests

```yaml
name: playwright-tests
on:
  workflow_dispatch:
  push:
jobs:
  playwright-tests:
    uses: button-inc/button-shared-gh-actions/.github/workflows/test-code-playwright.yml@develop
```

[Back to top](#github-actions-templates)

## Workflow Triggers

```yaml
# Set triggers to tell the workflow when it should run...
on:
  push:
    branches:
    - main
    paths-ignore:
    - 'README.md'
    - '.pre-commit-config.yaml'
    - './github/workflows/pre-commit-check.yaml'
  workflow_dispatch:

# A schedule can be defined using cron format.
  schedule:
    # * is a special character in YAML so you have to quote this string
    - cron:  '30 5,17 * * *'

    # Layout of cron schedule.  'minute hour day(month) month day(week)'
    # Schedule option to review code at rest for possible net-new threats/CVE's
    # List of Cron Schedule Examples can be found at https://crontab.guru/examples.html
    # Top of Every Hour ie: 17:00:00. '0 * * * *'
    # Midnight Daily ie: 00:00:00. '0 0 * * *'
    # 12AM UTC --> 8PM EST. '0 0 * * *'
    # Midnight Friday. '0 0 * * FRI'
    # Once a week at midnight Sunday. '0 0 * * 0'
    # First day of the month at midnight. '0 0 1 * *'
    # Every Quarter. '0 0 1 */3 *'
    # Every 6 months. '0 0 1 */6 *'
    # Every Year. '0 0 1 1 *'
    #- cron: '0 0 * * *'
  ...
```

[Back to top](#github-actions-templates)

## Secrets Management

The following repository secrets are required depending on which template is being used. [Learn more](https://docs.github.com/en/actions/security-guides/encrypted-secrets).

| Secret Name             | Description |
| :---------------------- | :------------|
| image-registry-user     | Registry username. Used for interacting with private image repositories.           |
| image-registry-password | Registry password. Used for interacting with private image repositories.       |
| gitleaks-license        | If you are scanning repos that belong to a GitHub personal account, then no license key is required. If you are scanning repos that belong to a GitHub organization account, then you'll have to obtain a free license [here](https://gitleaks.io/products.html).       |
| sonar_token             | Used when using the Sonar scanning templates.

[Back to top](#github-actions-templates)
## Specifying Action Versions and Branches

In your workflows, you can specify not only the version of a reusable action, but also the branch from which to pull the action. This is done by appending `@<branch>` or `@<version>` to the end of the action reference in your workflow file.

For instance, if you want to use the latest version of the playwright tests action from the `develop` branch, you would reference it like this:

```yaml
uses: button-inc/gh-actions/.github/workflows/test-code-playwright.yml@develop
```
This will always use the most recent version of the action from the `develop` branch. This can be useful when you want to test the latest changes that are not yet released. 

However, there might be situations where you want to use a specific version of an action. In such cases, you can specify the version number instead of the branch:

```yaml
uses: button-inc/gh-actions/.github/workflows/test-code-playwright.yml@v0.0.1
```
This ensures that your workflow uses a specific, known version of the action, providing stability and predictability. It's particularly useful when you want to ensure that your workflow doesn't break due to changes in the latest version.

[Back to top](#github-actions-templates)
## Keeping Your Actions Up-to-Date with Dependabot

Dependabot is a tool provided by GitHub that can automatically check for new versions of dependencies, including reusable GitHub Actions. You can configure Dependabot to periodically check for new versions of the actions used in your workflows, and open a pull request to update the version whenever a new one is available.

To set up Dependabot for your GitHub Actions, you'll need to add a new configuration file at `.github/dependabot.yml` in your repository. Here's an example configuration that checks for updates to GitHub Actions weekly:

```yaml
version: 2
updates:
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```
With this configuration, Dependabot will check for new versions of all GitHub Actions used in your repository every week. If a new version is found, Dependabot will open a pull request with the necessary changes to update to the new version.

Example of the pull request:
![image](https://github.com/button-inc/gh-actions/assets/72329369/cf245f90-1dc5-4916-b83a-a76a358b8161)


For more information on configuring Dependabot for GitHub Actions, see the [official documentation](https://docs.github.com/en/code-security/dependabot/working-with-dependabot/keeping-your-actions-up-to-date-with-dependabot).

[Back to top](#github-actions-templates)
## Reference

[Workflow Syntax](https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions)

[Workflow Triggers](https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows)

[Reusing workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)

[Dependabot auto-update actions](https://docs.github.com/en/code-security/dependabot/working-with-dependabot/keeping-your-actions-up-to-date-with-dependabot)

[Back to top](#github-actions-templates)
