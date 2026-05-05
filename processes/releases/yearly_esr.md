# Setting up yearly ESR

This is a *very* involved process with about a million things that can go wrong, so it's a safe assumption that this guide will be incomplete in places and may contain errors.

"Self-documenting" steps of the process, such as enablement bugs that can be completed easily using previous years' patches as a near-1:1 reference, are not given detailed steps in this guide.

## High-level timeline

### Two months out
1. Create meta bug and dependent enablement bugs
2. Create new ESR repository
3. Complete any other enablement bugs that are not mutually exclusive to a single ESR (e.g. can be completed without impacting previous ESR)

### One month out, *before* `comm-central -> comm-beta` merge
1. Setup initial Balrog rules
2. Update Thunderbird taskgraph on `comm-central` to support new ESR
3. Update previous ESR tree
	* At least one new version of the previous ESR must be released after updating aliases and before the first release of new ESR; otherwise, bouncer tasks will fail on the new ESR

### One week out, *after* `comm-central -> comm-beta` merge
1. Merge `comm-beta` to new ESR repository
2. Attempt build and promotion
	* It is ***highly unlikely*** that this first build will go smoothly; see troubleshooting section at the end of this guide for help with common issues

### Day of
1. Build, promote, push, and ship
2. Verify that files are available on FTP
3. Verify that `thunderbird.net` download link works
4. Setup final Balrog rules to allow updating from old ESR

## Setting up meta bug

1. Create a clone of previous year's meta bug (e.g. bug 1970846)
2. Add Firefox's new ESR meta bug as a dependent bug
3. Add each dependent bug from Firefox's meta bug that also accounts for Thunderbird's new ESR as well
4. For Firefox enablement bugs that don't account for Thunderbird, create clones of enablement bugs from previous year, such as (but not limited to):
	* Update Thunderbird taskgraph to support new ESR (e.g. bug 1972500)
	* Add new ESR to Searchfox (e.g. bug 1970809)
	* Create new ESR repository (e.g. bug 1971482)
	* Support new ESR in fxci-config (e.g. bug 1972938)

## Setup Initial Balrog rules

Rule updates for `esr` channel should be performed on Balrog staging *and* production.

### `esr` channel
1. Update version cutoff for current ESR to be less than the current ESR version plus one (e.g. for ESR 128, set it to `<129.0`)
	* This prevents updating beyond the current ESR for the time being
2. Create new ESR rule by duplicating the current ESR rule (should be the bottommost rule) and making the following updates:
	* Set Mapping and Fallback Mapping accordingly (new and current ESR, respectively)
	* Set background rate to zero
	* Decrease priority by 5
	* Update version cutoff to be less than the new ESR version plus one (e.g. for ESR 140, set it to `<141.0`)
	* Update alias to new ESR version (e.g. `thunderbird-esr128` to `thunderbird-esr140`)

### `esr-cdntest` channel
1. Update version cutoff for current ESR to be less than or equal to the current ESR version (e.g. for Thunderbird-128.12.0esr-build1, set it to `<=128.12.0`)
2. Create new ESR rule by duplicating the current ESR rule (should be the bottommost rule) and making the following updates:
	* Set Mapping and Fallback Mapping accordingly (new and current ESR, respectively)
	* Set background rate to 100
	* Decrease priority by 5
	* Update version cutoff to be less than the new ESR version plus one (e.g. for ESR 140, set it to `<141.0`)
	* Update alias to new ESR version (e.g. `thunderbird-esr128-cdntest` to `thunderbird-esr140-cdntest`)
	* Update channel of current ESR rule from `esr-cdntest*` to `esr-cdntest`

### `esr-localtest` channel
1. Update version cutoff for current ESR to be less than or equal to the current ESR version (e.g. for Thunderbird-128.12.0esr-build1, set it to `<=128.12.0`)
2. Create new ESR rule by duplicating the current ESR rule (should be the bottommost rule) and making the following updates:
	* Set Mapping and Fallback Mapping accordingly (new and current ESR, respectively)
	* Set background rate to 100
	* Decrease priority by 5
	* Update version cutoff to be less than the new ESR version plus one (e.g. for ESR 140, set it to `<141.0`)
	* Update alias to new ESR version (e.g. `thunderbird-esr128-localtest` to `thunderbird-esr140-localtest`)
	* Update channel of current ESR rule from `esr-localtest*` to `esr-localtest`

## Setup Final Balrog rules

Final Balrog updates are made on release day. Rule updates for `esr` channel should be performed on Balrog staging *and* production.

### `esr` channel
1. Update version cutoff for current ESR to be less than or equal to the current ESR version (e.g. for Thunderbird-128.12.0esr-build1, set it to `<=128.12.0`)
	* This allows current ESR users to update to the new ESR
2. Set background rate to pre-determined rate

## Updating Thunderbird taskgraph
	
Search all files in `taskcluster/` folder for references to previous ESR, such as the string `esr128`, and update them to new ESR.

### Non-exhaustive list of special cases

* `taskcluster/comm_taskgraph/transforms/update_verify_config.py`
	* Update `esr*-next` in `INCLUDE_VERSION_REGEXES` with new ESR version number (e.g. `esr128-next` to `esr140-next`)
	* Update `esr*-next` regex with new version number
* `taskcluster/kinds/merge-automation/kind.yml`
	* Update `upstream` of `release-to-esr` to new Firefox ESR upstream
* `taskcluster/kinds/release-bouncer-aliases/kind.yml`
	* Assign the following aliases to previous ESR:

	  ```yaml
	  thunderbird-esr-latest-ssl: installer-ssl
      thunderbird-esr-latest: installer
      thunderbird-esr-msi-latest-ssl: msi
      thunderbird-esr-msix-latest-ssl: msix
	  ```
	* Assign the following aliases to new ESR:
	
	  ```yaml
      thunderbird-esr-next-latest-ssl: installer-ssl
      thunderbird-esr-next-latest: installer
      thunderbird-esr-next-msi-latest-ssl: msi
      thunderbird-esr-next-msix-latest-ssl: msix
	  ```
* `taskcluster/kinds/release-balrog-scheduling/kind.yml`
	* Update `esr*` rule IDs (e.g. rule number of alias `thunderbird-esr140` from Balrog staging and production, as appropriate)
* `taskcluster/kinds/release-flatpak-push/kind.yml`
	* Remove ESR from `run-on-releases`, as the new ESR should not be pushed to Flathub until the Balrog update rate has reached 100
* `taskcluster/kinds/release-msix-push/kind.yml`
	* Remove ESR from `run-on-releases`, as the new ESR should not be pushed to the Microsoft Store until the Balrog update rate has reached 100
* `taskcluster/kinds/release-update-verify-config/kind.yml`
  `taskcluster/kinds/release-update-verify-config-next/kind.yml`
	* Update `last_watershed` to their base versions, respectively:
		* e.g. esr128: "128.0esr" and esr140: "140.0esr"
		* `release-update-verify` and `release-update-verify-next` tasks will only test versions greater than or equal to `last_watershed`

### Updating previous ESR taskgraph

When the new ESR tree is created, the previous ESR's tree needs to be updated in a few ways to make sure that certain tasks no longer run.

1. Update `release-bouncer-aliases` to match new ESR tree
2. Remove previous ESR from `run-on-releases` in `release-update-verify-next` and `release-update-verify-next`

## Performing merge to new tree

This section is <ins>**not yet complete and needs more detail**</ins>

1.  Clone new ESR repo
   
    <pre>hg clone comm-esr<i>[new ESR ver. #]</i> .</pre>

2.  Pull `comm-beta`
   
    <pre>hg pull comm-beta</pre>

3.  Perform graft from `comm-beta` to new ESR tree
   	* For example, to graft `comm-beta` into `comm-esr140`:
	   
      <pre>hg graft --tool internal:other <i>[child rev of BETA_139_END]</i>:<i>[child rev of RELEASE_140_BASE]</i></pre>

4.  Merge appropriate changeset range from comm-beta using `hg debugsetparents`
   
    <pre>hg debugsetparents <i>[merge tip of source repo] [tip rev of dest. repo from before merge]</i></pre>

5.  Commit merge

    <pre>hg commit -m "Merge old head via |hg debugsetparents <i>[merge tip of source repo] [tip rev of dest. repo from before merge]</i>| a=release CLOSED TREE DONTBUILD"</pre>

6.  Merge over old tags from `comm-beta` after using `hg debugsetparents`

7.  Verify that changeset hashes grafted into new ESR tree match those of `comm-beta`

8.  Pin to latest tag and revision of upstream ESR

9.  Push changes

10. Run `release-to-esr` merge-automation

## Verify results

* Build
	1. Check that branding is correct
* Promote
	1. Check that all files are present in new ESR's `candidates` folder on FTP
	2. Make sure all langpacks were pushed to FTP as well
* Ship
	1. Check that `thunderbird.net` ESR download link downloads new ESR

## Troubleshooting common problems

* Can't push to newly-created ESR repo
	1. Delete and re-clone the repo
	2. Re-run `mach bootstrap`
* Issues with `release-update-verify` or `release-update-verify-next` tasks
	1. Double-check `esr-localtest` Balrog rules
	2. Make sure `last_watershed` version is correct in `release-update-verify-config` and `release-update-verify-config-next` tasks
* Chain-of-trust errors
	1. Check values in `python/mozbuild/mozbuild/configure/constants.py`
