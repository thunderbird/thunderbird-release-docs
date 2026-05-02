Uplifts prepared -- (Corey)

Build day before:
- Check out comm-beta or update to tip
```
hg pull comm-beta
hg up tip
```
- Pin to Firefox

```
pin_for_release.py mozilla-beta
```

- Check For rust vendoring
```sh
./mach tb-rust check-upstream
```
- If neccessary, vendor
```sh
./mach tb-rust sync
./mach tb-rust vendor
```
- If you vendored rust, commit the change
```sh
hg commit -m "No Bug - Vendored Rust from <mozilla-repo>. r=release r+a=sking"
```
- Uplift bugs

```
graft_uplift.sh coreycb <commit hash from c-c>
```

- Verify recent additions

```
hg out -r . comm-beta
```


- Push those changes to comm-beta

```
hg push -r . comm-beta
```

- Follow up using Bugherder (must use full [https://hg.mozilla](https://hg.mozilla) URL)

Ship-it

•  Promote using hash of latest commit