# Monthly merges

## High-level timeline

1. Run dry runs a week out
2. Perform `comm-beta` -> `comm-release` merge
3. Perform `comm-central` -> `comm-beta` merge

## How to run actions on Treeherder

1. Find the push you want to run an action on
2. Click the arrow in the top right corner of the push
3. Select *Custom Push Action...* from the drop-down menu

## `comm-beta` -> `comm-release` merge

1. Email thunderbird-drivers that the merge is beginning using the template below, making sure to replace the bolded + italicized placeholders appropriately:

   ### Subject
   > Thunderbird beta merge & version bumps for Tue Sep ***MM*** ***YYYY*** (c-c -> ***VER*** / c-b -> ***VER***)

   ### Body
   > Hello!
   >
   > I'll be completing the Thunderbird beta merge today. The Firefox merges have not completed.
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
   >   Re-open comm-central, set comm-beta to approval needed
   >
   > Please let me know if you have any questions or comments.
   >
   > Thanks,

2. Close comm-beta in Treestatus
	1. Set **Status** to *Closed*
	2. Set **Reason Category** to *Merges*
	2. Set **Reason** to "Closed for comm-beta to comm-release merge"

3. Check if Rust needs to be re-vendored on `comm-release` using `mach tb-rust check-upstream`
	* If it does, make sure to re-vendor using `mach tb-rust vendor` then push

4. Run a dry run of the `merge-automation` custom push action using the payload below:
	
	```
	force-dry-run: true
	behavior: beta-to-release
	```

5. Verify the diff artifact looks correct

6. If everything is in order, rerun the previous custom push action, but with `force-dry-run` set to "false"

*Upon a successful run, `comm-beta` should get a version bump, branding changes, and two new tags:*

* *BETA\_**[PREVIOUS VERSION]**\_END*
* *BETA\_**[CURRENT VERSION]**\_BASE*

*However, .gecko_rev.yml is still not pinned to the correct tag/revision. This should be done during the next beta release.*
