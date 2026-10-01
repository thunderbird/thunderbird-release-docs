# Monthly merges

- [Running a custom push action](#running-a-custom-push-action)
- [main to beta merge](#main-to-beta-merge)
- [beta to release merge](#beta-to-release-merge)

## Running a custom push action

The merges are done with the `merge-automation` custom push action on
Treeherder. To run it:

1. Find the push you want to run the action on.
2. Click the arrow in the top right corner of the push.
3. Select **Custom Push Action** from the drop-down menu.

## main to beta merge

1. Email `thunderbird-drivers` that the merge is beginning, using the template
   below. Replace the `<PLACEHOLDERS>`.

   **Subject:**

   ```text
   Thunderbird main -> beta merge & version bumps for <DATE> (main -> <VERSION> / beta -> <VERSION>)
   ```

   **Body:**

   ```text
   Hello!

   I'll be completing the Thunderbird main -> beta merge today.

   main to <VERSION>, beta to <VERSION>

   The merge will be performed via automation.

   I'll keep people up to date by replying to this email:

   - Email before merge begins
   - Close the trees
   - Perform the merge
   - Email after the merge
   - Re-open main

   Please let me know if you have any questions or comments.

   Thanks,
   ```

2. Close `thunderbird-desktop-main` in
   [Treestatus](https://lando.moz.tools/treestatus/):
   1. Set **Status** to _Closed_.
   2. Set **Reason Category** to _Merges_.
   3. Set **Reason** to "Closed for main to beta merge".

3. Check if Rust needs to be re-vendored on `beta` with
   `mach tb-rust check-upstream`. If it does, re-vendor with
   `mach tb-rust vendor`, then push.

4. Do a dry run of the `merge-automation`
   [custom push action](#running-a-custom-push-action) with this payload:

   ```yaml
   force-dry-run: true
   behavior: main-to-beta
   ```

5. Verify that the diff artifact looks correct.

6. If everything is in order, run the action again with `force-dry-run` set to
   `false`.

   On success, `beta` gets a version bump, branding changes, and two new tags:

   - `BETA_<PREVIOUS_VERSION>_END`
   - `BETA_<NEW_VERSION>_BASE`

   `main` also gets a new tag:

   - `BETA_<NEW_VERSION>_BASE`

   > [!NOTE] `.gecko_rev.yml` is still not pinned to the correct tag/revision.
   > This is done during the next beta release.

7. Open the tip revision on `comm-beta` and verify that
   `mail/locales/l10n-changesets.json` has revisions.

8. Do a dry run of the `merge-automation` custom push action with this payload:

   ```yaml
   force-dry-run: true
   behavior: bump-main
   ```

9. If everything is in order, run the action again with `force-dry-run` set to
   `false`. On success, `main` gets a version bump and a new tag:

   - `NIGHTLY_<PREVIOUS_VERSION>_END`

10. Restore the trees in Treestatus:
    1. `main` to _Open_.

11. Tell the sheriffs in the
    [Thunderbird CI](https://matrix.to/#/#thunderbird-ci:mozilla.org) Matrix
    room that the tree is open again.

12. Reply to your first email using this template:

    ```text
    The merge is complete.

    main:

    - <TREEHERDER LINK TO VERSION BUMP REVISION>
    - <TREEHERDER LINK TO CHANGE TAGGED WITH NIGHTLY_*_END TAG>

    beta:

    - <TREEHERDER LINK TO TIP AFTER MERGE AND CONFIG UPDATE>

    Current tree status:

    - main: OPEN
    - beta: APPROVAL REQUIRED
    ```

## beta to release merge

1. Email `thunderbird-drivers` that the merge is beginning, using the template
   below. Replace the `<PLACEHOLDERS>`.

   **Subject:**

   ```text
   Thunderbird beta -> release merge & version bump for <DATE> (release -> <RELEASE_VER>)
   ```

   **Body:**

   ```text
   Hello!

   I'll be completing the Thunderbird beta -> release merge today.

   - elease to <RELEASE_VER>

   The merge will be performed via automation.

   I'll keep people up to date by replying to this email:

   - Email before merge begins
   - Close the trees
   - Perform the merge
   - Email after the merge

   Please let me know if you have any questions or comments.

   Thanks,
   ```

2. Close `thunderbird-desktop-beta` in
   [Treestatus](https://lando.moz.tools/treestatus/):
   1. Set **Status** to _Closed_.
   2. Set **Reason Category** to _Merges_.
   3. Set **Reason** to "Closed for beta to release merge".

3. Check if Rust needs to be re-vendored on `release` with
   `mach tb-rust check-upstream`. If it does, re-vendor with
   `mach tb-rust vendor`, then push.

4. Do a dry run of the `merge-automation`
   [custom push action](#running-a-custom-push-action) with this payload:

   ```yaml
   force-dry-run: true
   behavior: beta-to-release
   ```

5. Verify that the diff artifact looks correct.

6. If everything is in order, run the action again with `force-dry-run` set to
   `false`.

   On success, `release` gets a version bump, branding changes, and two new
   tags:

   - `RELEASE_<PREVIOUS_VERSION>_END`
   - `RELEASE_<NEW_VERSION>_BASE`

   `beta` also gets a new tag:

   - `RELEASE_<NEW_VERSION>_BASE`

7. Reply to your first email using this template:

   ```text
   The merge is complete.

   comm-beta:

   - <TREEHERDER LINK TO TIP>

   comm-release:

   - <TREEHERDER LINK TO TIP>

   Current tree status:

   - release: APPROVAL REQUIRED
   - beta: CLOSED
   ```
