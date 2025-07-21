# RENOVATE

Renovate is an open-source tool for managing library updates. 
It scans the repositories and detect when the libraries are outdated and creates the PRs.
Renovate can be configured with a GitHub app or a Bitbucket app or in a self-hosted way. The suggested way is using the 
repo apps. If you use the GitHub app the runs will be shown in the dashboard https://developer.mend.io/.

It only creates the PRs with relatives branches with the merge confidence, it's up to the developers to fix the issues
related to the updates.

Renovate support a large number of dependencies file like package.json or pom.xml.
If the dependencies file is not in the root it's possible to specify the paths of the files.

`
  "includePaths": [
    "frontend/package.json",
    "backend/pom.xml"
  ],
`

## RUN

The runs can be triggered:
* Automatically
* Manually
* GitHub action

### Automatically
The scan is automatically triggered when there is a push on configurations files like:
* `package.json`
* `yarn.lock`
* `pom.xml`
* `Dockerfile`
* `renovate.json`
Or when a PR is merged. In these cases the job will be shown in the renovate dashboard.

### Manually 
The job can be triggered manually from the renovate dashboard and the job will appear in the dashboard.

### GitHub action
Since renovate jobs cannot be triggered by scheduling this is possible via GitHub action.
This allows you to integrate the scan in the CI/CD process.
These jobs won't appear in the dashboard.

## CONFIG
Renovate allows the developer to set a large number of configurations. There three levels:
* **Global**: If you are using github create a repository called .github and put renovate.json in the root.
Repositories under your org or user account using the GitHub App will automatically inherit the global config from .github/renovate.json.
Bitbucket doesn't allow this but it possible to import a schema with the `"extends"`configuration.
* **Project**: file `renovate.json`
* **Package**: inside `packageRules`

### JSON example

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
      "matchPackageNames": [ -> Regex to the package
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
  "schedule": "after 17pm every day", -> Time range in which the PRs can be openened
  "labels": [ -> The label to add in the PR
    "renovate"
  ]
}
```
