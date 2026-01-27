## Cross compile Windows x86_64 on Linux x86_64

These steps were tested in a full system container running Ubuntu 24.04 on an Ubuntu 24.04 host.

It's risky to cross-compile on a system that you use for other purposes. The full system container
is one way to isolate the build to it's own system.

**On a Linux distribution that supports incus, start a full system container and log in:**
```
incus launch -p default -p incus-gui -c limits.memory=16GiB -c limits.cpu=8 images:ubuntu/24.04 thunderbuild
incus exec thunderbuild -- sudo --user ubuntu --login
```

**All of the following steps are performed on the container OS.**

**Install prerequisite packages:**
Note: Not all of these are needed. If you have the time, please try trimming to the minimum required.
```
sudo apt update && sudo apt install -y \
  build-essential clang cmake lld llvm clang-tools \
  curl git mercurial python3 python3-venv python3-pip \
  autoconf2.13 unzip zip yasm nasm nodejs npm wget unzip zip \
  ccache sccache pkg-config \
  msitools cabextract \
  wine64
```

**Add rust Windows target:**
```
rustup default stable
rustup target add x86_64-pc-windows-msvc
```

**Clone source:**
```
hg clone https://hg.mozilla.org/mozilla-central source/
hg clone https://hg.mozilla.org/comm-central source/comm/
```

**Update mozconfig:**
```
ac_add_options --enable-project=comm/mail
ac_add_options --target=x86_64-pc-windows-msvc

# Similar knobs you used
ac_add_options --enable-bootstrap
ac_add_options --disable-debug
ac_add_options --disable-tests
ac_add_options --with-ccache=sccache
```

**Build:**
```
./mach bootstrap # choose option 2
./mach build
```

**Produce distributable artifacts:**
```
./mach package
```
