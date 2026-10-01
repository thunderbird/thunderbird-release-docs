# Manually vendor a Rust crate changed by Firefox releng

In this case, Firefox releng changed the files in the crate from CRLF to LF
endings because the person working on the issue was using Windows. Doesn't seem
like a good reason to do that, but anyway...

## Steps

These are the steps used in this case, for the `remove_dir_all` (v0.5.3) crate.

1. In the Firefox source directory, check out the revision in
   `comm/.gecko_rev.yml`:

   ```sh
   cd source
   git checkout <hash>
   ```

2. Update `Cargo.lock` to the correct upstream checksum. In this case, the
   checksum for `remove_dir_all` (v0.5.3) had to be changed to the
   [upstream checksum](https://github.com/rust-lang/crates.io-index/blob/master/re/mo/remove_dir_all#L7):

   ```text
   3acd125665422973a33ac9d3dd2df85edad0f4ae9b00dafb1a05e43a9f5ef8e7
   ```

3. Vendor:

   ```sh
   ./mach tb-rust vendor
   ```

4. Go to `comm`:

   ```sh
   cd comm
   ```

   Then update `rust/Cargo.lock` with the checksum that mozilla-release was
   using. This checksum matches their local files instead of upstream.

5. Copy the vendored crate into `comm`, then commit it:

   ```sh
   rsync -av ../third_party/rust/remove_dir_all/ ./third_party/rust/remove_dir_all/
   git add -A .
   git commit -m "No bug - Vendored rust from mozilla-release. r+a=coreycb"
   ```

6. Go back to the Firefox source directory, throw away the changes, and return
   to the branch you were on before step 1:

   ```sh
   cd ..
   git reset --hard
   git clean -fd
   git switch -
   ```
