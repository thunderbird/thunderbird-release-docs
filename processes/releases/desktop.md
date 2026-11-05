# Desktop releases

Uplifts are approved by Corey.

## The day before the build

### Update the branch

Check out the branch for the release and update it to tip:

```sh
git pull origin <branch>
```

### Pin to Firefox

1. Put our
   [pin.sh](https://github.com/thunderbird/thunderbird-release-tools/blob/main/releases/scripts/pin.sh)
   script in your `comm` checkout and run it.
2. It updates `.gecko_rev.yml` and creates a commit. Check the commit to see
   which Firefox tag or hash it pinned to.
3. In the source directory (`gecko`), check out that tag or hash:

   ```sh
   git checkout <tag/hash>
   ```

> [!NOTE] For the final beta of a cycle, pin to tip instead of a tag. See
> [Final beta: pin to tip](#final-beta-pin-to-tip).

### Re-vendor Rust (if needed)

Check whether Rust needs to be re-vendored:

```sh
./mach tb-rust check-upstream
```

If it does, re-vendor and commit:

```sh
./mach tb-rust vendor
git commit -m "No Bug - Vendored Rust from firefox-<branch>. r=release r+a=ebaginski"
```

### Uplift patches

1. Put our
   [uplift.sh](https://github.com/thunderbird/thunderbird-release-tools/blob/main/releases/scripts/uplift.sh)
   script in your `comm` checkout.
2. Uplift each patch and check the commit it creates:

   ```sh
   ./uplift.sh coreycb <hash>
   git log -n 1
   ```

### Push the uplifts

```sh
lando push-commits --lando-repo thunderbird-desktop-<branch>
```

Once the changes land, use [Bugherder](https://bugherder.mozilla.org) to update
the corresponding bugs.

## Ship it

Promote the release using the hash of the latest commit.

## Final beta: pin to tip

The final beta of a cycle (usually b4) is our last chance to test Firefox's
final beta changes before they ship in the Release channel the following
Tuesday.

The timing doesn't line up with Firefox's:

- We build the final beta on **Tuesday** so it can be tested.
- We release it and merge beta → release on **Wednesday**.
- Firefox often doesn't tag its final beta until **Wednesday**.

If we pin to the latest Firefox beta tag on Tuesday, we can miss changes (often
security fixes) that land on `mozilla-beta` after that tag. We then have to
build an extra beta (for example, b5) to pick them up.

To avoid that, pin the final beta to the **tip** of `mozilla-beta` instead of a
tag.

### Before the build (Tuesday)

1. Check the
   [mozilla-beta pushlog](https://hg-edge.mozilla.org/releases/mozilla-beta) for
   recent activity. Changes are still likely if the last push was only a few
   hours ago.
2. Build as late in the day as is practical. This gives late Firefox fixes more
   time to land.
3. Pin to tip instead of a tag. `pin.sh` only pins to tags, so edit
   `.gecko_rev.yml` by hand:
   - Set `GECKO_HEAD_REV` to the latest revision in the pushlog.
   - Comment out `GECKO_HEAD_REF`.

   Commit the change, for example:

   ```text
   No bug - Pin to mozilla-beta tip (<rev12>). r+a=release
   ```

4. Note the `mozilla-beta` revision you pinned to.

### After the build (Wednesday, before release)

1. Check the mozilla-beta pushlog again.
2. Compare the revision we pinned to with Firefox's final beta tag:
   - **Tag points at our pinned revision, or nothing landed after it:** we have
     every change. Go ahead with the release.
   - **New changes landed after our pinned revision:** decide whether they
     matter. If they do (for example, security fixes), we need another beta.

> [!NOTE] Pinning to tip won't work every time. If Firefox lands fixes late on
> Tuesday or on Wednesday, we still need another beta. It does cover the common
> case where the last Firefox change lands earlier on Tuesday.

> [!NOTE] `pin.sh` doesn't update a commented-out `GECKO_HEAD_REF`. Before
> pinning the next beta, uncomment `GECKO_HEAD_REF` in `.gecko_rev.yml`, then
> run `pin.sh`.
