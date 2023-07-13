# Github Actions Templates

This project contains all Github Actions templates. To make use of the repository, fork this repository and modify the `env` and `trigger` sections of each template to meet the needs of your application or repository.

- [Workflow Templates](#workflow-templates)
  - [Build Push Docker](#docker-build-push)
  - [Trivy Scan](#trivy-scan)
  - [SonarCloud Scan](#sonar-repo-scan)
  - [Husky Scan](#husky-scan)
  - [Gitleaks Scan](#gitleaks-scan)
  - [Playwright Tests](#playwright-tests)
- [Secrets Management](#secrets-management)
- [Workflow Triggers](#workflow-triggers)
- [Testing Framework](#testing-framework)
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


## Reference

[Workflow Syntax](https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions)

[Workflow Triggers](https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows)

[Reusing workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)

[Back to top](#github-actions-templates)