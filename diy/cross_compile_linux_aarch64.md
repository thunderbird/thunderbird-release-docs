## Cross compile Linux aarch64 on Linux x86_64

These steps were tested in a full system container running Ubuntu 24.04 on an Ubuntu 24.04 host.

It's risky to cross-compile on a system that you use for other purposes. There's potential for
x86_64 packages getting uninstalled when installing aarch64 packages. The full system container
is one way to isolate the build to it's own system.

**On a Linux distribution that supports incus, start a full system container and log in:**
```
incus launch -p default -p incus-gui -c limits.memory=16GiB -c limits.cpu=8 images:ubuntu/24.04 thunderbuild
incus exec thunderbuild -- sudo --user ubuntu --login
```

**All of the following steps are performed on the container OS.**

**Add arm64 package support:**
```
sudo dpkg --add-architecture arm64
```

**Update package repositories that are configured in /etc/apt/sources.list.d/ubuntu.sources (for new Ubuntu installs):**
```
# amd64 (host)
Types: deb
URIs: http://us.archive.ubuntu.com/ubuntu/
Suites: noble noble-updates noble-backports
Components: main restricted universe multiverse
Architectures: amd64
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

Types: deb
URIs: http://security.ubuntu.com/ubuntu
Suites: noble-security
Components: main restricted universe multiverse
Architectures: amd64
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

# arm64 (target)
Types: deb
URIs: http://ports.ubuntu.com/ubuntu-ports/
Suites: noble noble-updates noble-backports
Components: main restricted universe multiverse
Architectures: arm64
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

Types: deb
URIs: http://ports.ubuntu.com/ubuntu-ports/
Suites: noble-security
Components: main restricted universe multiverse
Architectures: arm64
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
```

**Or update package repositories that are configured in /etc/apt/sources.list (older Ubuntu installs):**
```
deb [arch=amd64] http://archive.ubuntu.com/ubuntu noble main restricted universe multiverse
deb [arch=amd64] http://archive.ubuntu.com/ubuntu noble-updates main restricted universe multiverse
deb [arch=amd64] http://security.ubuntu.com/ubuntu noble-security main restricted universe multiverse
deb [arch=arm64] http://ports.ubuntu.com/ubuntu-ports noble main restricted universe multiverse
deb [arch=arm64] http://ports.ubuntu.com/ubuntu-ports noble-updates main restricted universe multiverse
deb [arch=arm64] http://ports.ubuntu.com/ubuntu-ports noble-security main restricted universe multiverse
```

**Install prerequisite packages:**
```
sudo apt update && sudo apt install -y \
  mercurial build-essential cbindgen ccache clang clang-tools curl git htop libclang-dev lld llvm-dev \
  python3 python3-venv python3-pip sccache wget pkg-config unzip zip autoconf2.13 yasm nasm nodejs npm rustup \
  gcc-aarch64-linux-gnu g++-aarch64-linux-gnu libstdc++-12-dev-arm64-cross binutils-aarch64-linux-gnu libc6-dev-arm64-cross \
  libc6:arm64 libc6-dev:arm64 libstdc++-13-dev:arm64 libgcc-13-dev:arm64 \
  zlib1g-dev:arm64 libffi-dev:arm64 libglib2.0-dev:arm64 libdbus-1-dev:arm64 \
  libgtk-3-dev:arm64 libx11-dev:arm64 libxext-dev:arm64 libxt-dev:arm64 \
  libxcomposite-dev:arm64 libxdamage-dev:arm64 libxfixes-dev:arm64 libxrandr-dev:arm64 \
  libxrender-dev:arm64 libxkbcommon-dev:arm64 libxshmfence-dev:arm64 libdrm-dev:arm64 \
  libwayland-dev:arm64 libegl1-mesa-dev:arm64 libgl1-mesa-dev:arm64 \
  libpci-dev:arm64 libpulse-dev:arm64 libasound2-dev:arm64
```

**Add rust aarch64 support:**
```
rustup default stable
rustup target add aarch64-unknown-linux-gnu
```

**Various verifications:**
```
rustc --print target-list | grep aarch64
clang --version
aarch64-linux-gnu-gcc -v
rustup show
ls /usr/aarch64-linux-gnu
ls /usr/aarch64-linux-gnu/lib/libc.so
ls /usr/lib/aarch64-linux-gnu/pkgconfig/alsa.pc
```

**Clone source:**
```
hg clone https://hg.mozilla.org/mozilla-central source/
hg clone https://hg.mozilla.org/comm-central source/comm/
```

**Update mozconfig:**
```
ac_add_options --enable-project=comm/mail
ac_add_options --with-ccache=sccache
ac_add_options --target=aarch64-linux-gnu
ac_add_options --enable-bootstrap
ac_add_options --disable-debug
ac_add_options --disable-tests

# Environment for cross pkg-config (exported into build)
mk_add_options "export PKG_CONFIG_LIBDIR=/usr/lib/aarch64-linux-gnu/pkgconfig:/usr/lib/pkgconfig:/usr/share/pkgconfig"
```

**Build:**
```
./mach bootstrap # choose option 2
./mach build
```
