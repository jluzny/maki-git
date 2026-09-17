# Maintainer: Jiri Luzny <jiri.luzny@gmail.com>
# VCS package: builds maki from the upstream git repository (tip of default branch).
# Upstream: https://github.com/tontinton/maki
pkgname=maki-git
pkgver=0.5.5.r5.g5da1b2a
pkgrel=1
pkgdesc='An efficient AI coding agent. Native Rust TUI, extendable with Neovim-like Lua plugins'
arch=('x86_64' 'aarch64')
url='https://github.com/tontinton/maki'
license=('MIT')
depends=('gcc-libs' 'glibc' 'zlib')
makedepends=('cargo' 'git' 'perl' 'python')
optdepends=('rtk: filter bash output to save ~50% of those tokens')
provides=("maki=$pkgver")
conflicts=('maki' 'maki-bin')
# isahc vendors OpenSSL + curl, mlua builds Luau, and the grammar crates build
# C/C++. With makepkg's default LTO, those native objects carry GCC LTO bytecode
# that rustc's LLVM lld cannot link (undefined curl_*/lua_* symbols at the final
# link), so build the vendored C/C++ without LTO.
options=('!lto')
source=("${pkgname}::git+${url}.git")
sha256sums=('SKIP')

pkgver() {
  cd "$pkgname"
  git describe --long --tags --abbrev=7 |
    sed 's/^v//;s/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
  cd "$pkgname"
  # Fetch registry and git dependencies (monty, patched crossterm) now, so the
  # build can run fully offline via --frozen.
  cargo fetch --locked
}

build() {
  cd "$pkgname"
  export CARGO_TARGET_DIR=target
  cargo build --release --frozen
}

package() {
  cd "$pkgname"
  install -Dm0755 target/release/maki "$pkgdir/usr/bin/maki"
  install -Dm0644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}
