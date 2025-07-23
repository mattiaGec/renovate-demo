# RENOVATE

Renovate is an open-source tool for managing library updates.
It scans your repository, detects outdated dependencies, and creates pull requests (PRs) to update them.
Renovate can be integrated using a repo app like GitHub App and Bitbucket App or a self-hosted setup.
The recommended approach is to use the GitHub or Bitbucket App.
If you use the GitHub/Bitbucket app the runs will be shown in the dashboard https://developer.mend.io/.

Renovate only creates PRs—it's up to developers to address any issues caused by updated dependencies.

## SUPPORTED FILES
Renovate supports many dependency file formats, including:
* package.json
* pom.xml
* Dockerfile
* GitHub action workflows

If your dependency files are not in the root directory, you can specify their paths:
``` json
  "includePaths": [
    "frontend/package.json",
    "backend/pom.xml"
  ],
```

## RUN MODES
Renovate runs can be triggered in three ways:

### Automatically
Triggered by:
* a push to config or dependency files (e.g., package.json, pom.xml, renovate.json)
* a merged PR
These runs are visible in the Mend dashboard.

### Manually 
Jobs can be triggered directly from the Mend dashboard (if using GitHub/Bitbucket App).

### GitHub action
To run Renovate on a schedule or as part of your CI/CD pipeline,
These jobs won't appear in the dashboard.

## CONFIGURATION
Renovate allows the developer to set a large number of configurations. 
There are three configuration levels:
* **Global**: Create a repository called .github and place renovate.json in the root.
All repositories under your org or user account using the GitHub App will automatically inherit the global configuration.
Bitbucket doesn't allow this but it possible to import a schema with the `"extends"`configuration.
* **Project**: Add `renovate.json` to the repository root.
* **Package**: Use `packageRules` to define rules for specific packages.

### JSON CONFIG EXAMPLE

```jsonc
{
  "extends": ["config:best-practices"], -> configuration preset to import
  "includePaths": [ -> paths to the dependencies file
    "package.json"
  ],
  "baseBranches": ["main"], -> Allow to specify on which branch run the job
  "prCreation": "immediate", -> When the PR is created
  "prHourlyLimit": 0, -> Number of PR per hour. Option, 0 is disabled
  "prConcurrentLimit": 8, -> Number of PR open at the same time
  "rangeStrategy": "bump", -> Update strategy
  "packageRules": [ -> Allow to write specific rules per package
    {
      "groupName": "redux - minor ", -> Rule name
      "matchPackageNames": [ -> Names to the package
        "redux"
      ],
      "matchUpdateTypes": [ -> Which update types are involved
        "patch",
        "minor"
      ],
      "enabled": true -> Will  create the PR for patch or minor
    },
    {
      "groupName": "redux - major",
      "matchPackageNames": [
        "redux"
      ],
      "matchUpdateTypes": [
        "major"
      ],
      "enabled": false -> Won't create the PR for patch or minor
    }
  ],
  "timezone": "Europe/Zurich",
  "automerge": false, -> If you want to automerge the PR without review
  "schedule": ["after 5pm every weekday"], -> Time range in which the PRs can be openened
  "labels": [ -> The label to add in the PR
    "renovate"
  ]
}
```

## BEST PRACTICES
* Use the config:best-practices preset (instead of the config:recommended preset). It includes:
``` jsonc
{
  "extends": [
    "config:recommended",
    "docker:pinDigests",
    "helpers:pinGitHubActionDigests",
    ":configMigration",
    ":pinDevDependencies",
    "abandonments:recommended"
  ]
}
```
* Keep dependencies updated regularly.
* Review PRs carefully—especially major updates.

## PROS
* Automated Dependency Management
  * Automatically opens PRs when updates are available (daily, weekly, or custom schedule). 
  * Keeps your dependencies up-to-date without manual checking.

* Supports Many Ecosystems
  * Works with JavaScript (package.json), Java (pom.xml), Dockerfiles, GitHub Actions, Python (requirements.txt), Go modules, and more.

* Merge Confidence (when using Mend dashboard)
  * PRs include metadata about how widely tested the update is in the community.

* Security Updates
  * Can be configured to prioritize security updates (e.g., CVEs or GitHub advisories).

* Integrates with GitHub / GitLab / Bitbucket
  * GitHub App works out of the box, no self-hosting needed.
  * Supports dashboards, audit logs, and automatic inheritance of shared config.

* Self-Hosting Option
  * Useful if you want to avoid external services.

## CONS
* Complex Configuration (at first)
  * Many config options = powerful but overwhelming.

* Only about versions
  * Renovate doesn’t "understand" your code — it just bumps versions.
  * It won’t tell you if a major update breaks functionality or has side effects.

* Merge Confidence Not Available in All Modes
  * It' only available using GitHub App. It's not supported for self-hosting or GitHub Actions

* Can Slow Down CI/CD
  * If you don’t limit PRs, your pipeline may get slower due to too many Renovate PR builds.
