**Manually syncing a Rust dependency**

In this case, firefox releng changed the files in the crate from CRLF to LF endings. See bug: https://bugzilla.mozilla.org/show_bug.cgi?id=1975052

**Steps to sync in this case were:**

1.  `cd source`
2.  Run `hg up <hash>` with the hash that maps to what's in comm/.gecko_rev.yml
3.  Update source/Cargo.lock to the correct upstream checksum. In this case we had to update the checksum for remove_dir_all (v0.5.3) to the real upstream checksum, 3acd125665422973a33ac9d3dd2df85edad0f4ae9b00dafb1a05e43a9f5ef8e7, according to  
    https://github.com/rust-lang/crates.io-index/blob/master/re/mo/remove_dir_all#L7
4.  `./mach tb-rust vendor`
5.  `cd comm`
6.  Update rust/Cargo.lock with the checksum that mozilla-release was using (this checksum matches their local files instead of upstream)
7.  `cp -R ../third_party/rust/remove_dir_all/ ./third_party/rust/remove_dir_all/`
8.  `hg addremove .`
9.  Update `mc_cargo_lock` in rust/checksums.json with the SHA512 hash of upstream Cargo.lock file
	- On Linux, you can use `sha512sum` to generate the hash
10. `hg commit -m "No bug - Vendored rust from mozilla-release. r+a=coreycb"`
11. `cd ..`
12. `hg update -C; hg purge`
