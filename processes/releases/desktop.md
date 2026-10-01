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
