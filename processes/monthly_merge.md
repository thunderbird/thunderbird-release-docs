# Monthly merges

## Things to Know Beforehand

### How to run actions on Treeherder

1. Find the push you want to run an action on
2. Click the arrow in the top right corner of the push
3. Select *Custom Push Action...* from the drop-down menu


## High-level timeline

1. Run dry runs a week out
2. Perform `comm-beta` -> `comm-release` merge
3. Perform `comm-central` -> `comm-beta` merge


***

## `comm-central` -> `comm-beta` merge

1. Email thunderbird-drivers and sheriffs mailing lists that the merge is beginning using the template below, making sure to replace the bolded + italicized placeholders appropriately:

   ### Subject
   > Thunderbird comm-central -> comm-beta merge & version bumps for Tue Sep ***MM*** ***YYYY*** (c-c -> ***VER*** / c-b -> ***VER***)

   ### Body
   > Hello!
   >
   > I'll be completing the Thunderbird comm-central -> comm-beta merge today. The Firefox merges have not completed.
   >
   >   comm-central to ***VER***
   >   comm-beta to ***VER***
   >
   > The merge will be performed via automation.
   >
   > I’ll keep people up to date by replying to this email:
   >
   >   Email before merge begins
   >   Close the trees
   >   Perform the merge
   >   Email after the merge
   >   Re-open comm-central, set comm-beta to approval needed
   >
   > Please let me know if you have any questions or comments.
   >
   > Thanks,

2. Close comm-central in Treestatus
	1. Set **Status** to *Closed*
	2. Set **Reason Category** to *Merges*
	3. Set **Reason** to "Closed for comm-central to comm-beta merge"

3. Check if Rust needs to be re-vendored on `comm-beta` using `mach tb-rust check-upstream`
	* If it does, make sure to re-vendor using `mach tb-rust vendor` then push

4. Run a dry run of the `merge-automation` custom push action using the payload below:

	```
	force-dry-run: true
	behavior: main-to-beta
	```

5. Verify the diff artifact looks correct

6. If everything is in order, rerun the previous custom push action, but with `force-dry-run` set to "false"

   *Upon a successful run, `comm-beta` should get a version bump, branding changes, and two new tags:*

   * *BETA\_**[PREVIOUS VERSION]**\_END*
   * *BETA\_**[NEW VERSION]**\_BASE*

   *However, .gecko_rev.yml is still not pinned to the correct tag/revision. This should be done during the next beta release.*

   *`comm-central` should also get a new tag:*

   * *BETA\_**[NEW VERSION]**\_BASE*

7. Open the tip revision on comm-beta and verify that `mail/locales/l10n-changesets.json` has revisions

8. Run a dry run of the `merge-automation` custom push action using the payload below:

	```
	force-dry-run: true
	behavior: bump-main
	```

9. If everything is in order, rerun the previous custom push action, but with `force-dry-run` set to "false"

   *Upon a successful run, `comm-central` should get a version bump and a new tag:*

   * *NIGHTLY\_**[PREVIOUS VERSION]**\_END*

10. Restore trees in Treestatus
   1. `comm-central` to OPEN
   2. `comm-beta` to APPROVAL REQUIRED

11. Reply to your previous email using the template below:

   > Merges are finished.
   >
   > Thunderbird ***VER*** will be built once the Firefox build has been tagged in Mercurial.
   >
   > comm-central:
   >
   > * ***TREEHERDER LINK TO VERSION BUMP PUSH REVISION***
   > * ***TREEHERDER LINK TO MERGE PUSH REVISION***
   >
   > comm-beta:
   >
   > * ***TREEHERDER LINK TO TIP***
   >
   > Documentation:
   >
   > * No changes
   >
   > Current tree status:
   >
   > * comm-central: OPEN
   > * comm-beta: APPROVAL REQUIRED


***

## `comm-beta` -> `comm-release` merge

1. Email thunderbird-drivers mailing list that the merge is beginning using the template below, making sure to replace the bolded + italicized placeholders appropriately:

   ### Subject
   > Thunderbird comm-beta -> comm-release merge & version bumps for Tue Sep ***MM*** ***YYYY*** (c-b -> ***VER*** / c-r -> ***VER***)

   ### Body
   > Hello!
   >
   > I'll be completing the Thunderbird comm-beta -> comm-release merge today. The Firefox merges have not completed.
   >
   >   comm-beta to ***VER***
   >   comm-release to ***VER***
   >
   > The merge will be performed via automation.
   >
   > I’ll keep people up to date by replying to this email:
   >
   >   Email before merge begins
   >   Close the trees
   >   Perform the merge
   >   Email after the merge
   >   comm-beta will remain closed until next week's comm-central -> comm-beta merge
   >
   > Please let me know if you have any questions or comments.
   >
   > Thanks,
 
2. Close comm-beta in Treestatus
	1. Set **Status** to *Closed*
	2. Set **Reason Category** to *Merges*
	3. Set **Reason** to "Closed for comm-beta to comm-release merge"

3. Check if Rust needs to be re-vendored on `comm-release` using `mach tb-rust check-upstream`
	* If it does, make sure to re-vendor using `mach tb-rust vendor` then push

4. Run a dry run of the `merge-automation` custom push action using the payload below:
	
	```
	force-dry-run: true
	behavior: beta-to-release
	```

5. Verify the diff artifact looks correct

6. If everything is in order, rerun the previous custom push action, but with `force-dry-run` set to "false"

   *Upon a successful run, `comm-release` should get a version bump, branding changes, and two new tags:*

   * *RELEASE\_**[PREVIOUS VERSION]**\_END*
   * *RELEASE\_**[NEW VERSION]**\_BASE*

   *`comm-beta` should also get a new tag:*

   * *RELEASE\_**[NEW VERSION]**\_BASE*

7. Reply to your previous email using the template below:

   > Merges are finished.
   >
   > Thunderbird ***VER*** will be built once the Firefox build has been tagged in Mercurial.
   >
   > comm-beta:
   >
   > * ***TREEHERDER LINK TO TIP***
   >
   > comm-release:
   >
   > * ***TREEHERDER LINK TO TIP***
   >
   > Documentation:
   >
   > * No changes
   >
   > Current tree status:
   >
   > * comm-release: APPROVAL REQUIRED
   > * comm-beta: CLOSED
