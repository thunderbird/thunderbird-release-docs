Uplifts prepared -- (Corey)

Build day before: 
- Check out comm-beta or update to tip
```
hg pull <comm repo>
hg up tip
```
- Pin to Firefox

```
pin_for_release.py <mozilla repo>
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
hg out -r . <comm repo>
```


- Push those changes to the appropriate comm repo

```
hg push -r . <comm repo>
```

- Follow up using Bugherder (must use full [https://hg.mozilla](https://hg.mozilla) URL)

Ship-it

•  Promote using hash of latest commit