# Automating Microsoft Store Uploads for Thunderbird

## Overview

Thunderbird uses **MSIX** instead of MSI for publishing to the Microsoft Store. Several components and tasks must be configured to support MSIX pushing in Taskcluster, modeled after existing Flatpak support.

## Key Components

### 1. Required Modifications

#### Kind file and transform 

- A [kind file](https://searchfox.org/comm-central/source/taskcluster/kinds/release-msix-push/kind.yml)
  in `comm-central` defines the Thunderbird-specific push task.
- Reference examples from Firefox:
  - [Firefox kind.yml](https://github.com/mozilla/gecko-dev/blob/master/taskcluster/kinds/release-msix-push/kind.yml)
  - [Firefox MSIX push transform](https://github.com/mozilla/gecko-dev/blob/master/taskcluster/gecko_taskgraph/transforms/release_msix_push.py)
    - The Firefox transform may need changes to support Thunderbird.
- Reference examples from Flatpak:
  - [Flatpak kind.yml](https://hg.mozilla.org/comm-central/file/tip/taskcluster/kinds/release-flatpak-push/kind.yml)
  - [Flatpak transform](https://github.com/mozilla/gecko-dev/blob/master/taskcluster/gecko_taskgraph/transforms/release_flatpak_push.py)

#### Scriptworker

- `scriptworker-scripts` defines a special Docker image  that does the actual MSIX pushing:
  - [PushMSIX Script](https://github.com/mozilla-releng/scriptworker-scripts/tree/master/pushmsixscript)
    - This contains all of the scriptworkers used by mozilla and thunderbird.
    - See README for pushmsixscript. We need to have them define a new worker type for “comm” (they may
      spin up a level 1 and level 3 worker)
    - [PR adding Thunderbird support](https://github.com/mozilla-releng/scriptworker-scripts/pull/775)

#### Secrets / Credentials
- Keys such as `TENANT_ID`, `CLIENT_ID`, and `CLIENT_SECRET` added to `sops` by Heitor:
  - [PR Discussion](https://github.com/mozilla-releng/scriptworker-scripts/pull/775#issuecomment-1660939342)
- Azure AD and Partner Center integration. See details on how the Azure AD Tenant is associated with Partner center at:
  - [Store Submission API Docs](https://learn.microsoft.com/en-us/windows/apps/publish/store-submission-api)
  - [Azure AD Overview](https://portal.azure.com/#view/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/~/Overview)
  - [Microsoft Partner Center](https://partner.microsoft.com/en-us/dashboard/account/v3/tenantmanagement#commercial)

#### Permissions and Grants

- Thunderbird’s MSIX push permission is controlled via [`fxci-config`](https://github.com/mozilla-releng/fxci-config).
  - fxci-config grants the permissions the Thunderbird task needs to call the scriptworker script
  - See `Grants.yaml` - this is where comm branches that define Thunderbird’s taskgraph are granted access to do things such as push to MSIX or Flathub
  - Grants for Thunderbird pushmsixscript support already added at:
    - [Bugzilla #1817658](https://bugzilla.mozilla.org/show_bug.cgi?id=1817658)
    - [Phabricator D185601](https://phabricator.services.mozilla.com/D185601)

### 2. Testing Notes

- Relevant resources:
  - [MSIX script config example](https://github.com/mozilla-releng/scriptworker-scripts/blob/master/pushmsixscript/examples/config.example.json)
- `scriptworker` arguments are stored in a **cloudops repo** (access not available).
- The staging scriptworker doesn't actually push. Regardless the try server doesn't have permission to push anything.
- When testing with a try build, a lot of the release-prefixed tasks aren't available to run in taskcluster.
- Select "Add new jobs (search)" and specify release-flatpak-push, for example. You’ll need to commit a try_task_config.json
  file to the root directory of comm-central, commit the changes, and push to try:
  - `hg push-to-try -s ssh://hg.mozilla.org/try-comm-central -m "try_task_config.json"`

#### Example `try_task_config.json`:
```json
{
  "parameters": {
    "optimize_target_tasks": true,
    "release_type": "esr128",
    "target_tasks_method": "try_cc_tasks",
    "try_mode": "try_task_config",
    "try_task_config": {
      "tasks": [
        "release-msix-push-thunderbird"
      ]
    }
  },
  "version": 2
}
```

## Related Work

- RelEng tracking bug: [Bug 1931409](https://bugzilla.mozilla.org/show_bug.cgi?id=1931409)
