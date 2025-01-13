# Maintainer: David Runge <dvzrv@archlinux.org>

pkgname=niri
pkgver=25.01
pkgrel=1
pkgdesc="A scrollable-tiling Wayland compositor"
arch=(x86_64)
url="https://github.com/YaLTeR/niri"
license=(GPL-3.0-or-later)
depends=(
  cairo
  gcc-libs
  glib2
  glibc
  libdisplay-info
  libinput
  libpipewire
  libxkbcommon
  mesa
  pango
  pixman
  seatd
  systemd-libs
  xdg-desktop-portal-impl
)
makedepends=(
  clang
  rust
)
optdepends=(
  'alacritty: a suggested GPU-accelerated terminal emulator'
  'bash: for niri-session script'
  'fuzzel: a suggested Wayland application launcher'
  'mako: a suggested Wayland notification daemon'
  'org.freedesktop.secrets: for apps to rely on secrets portal'
  'swaybg: a suggested Wayland wallpaper tool'
  'waybar: a suggested Wayland customizable desktop bar'
  'waylock: a suggested Wayland screen locker'
  'xdg-desktop-portal-gtk: a suggested XDG desktop portal'
  'xdg-desktop-portal-gnome: a XDG desktop portal required for screencasting'
)
# NOTE: linking issues with LTO enabled
options=(!lto)
source=($pkgname-$pkgver.tar.gz::$url/archive/refs/tags/v$pkgver.tar.gz)
sha512sums=('5c555f4410659a4301151a58938cc9880a9a244af138157c0be0fb38f0e82d20e6fccccad7e47ef515d3aaa0f7ddeef5088adad3f8d0be1a99a89f822c8b7a66')
b2sums=('b8d1b1919cc5568b38ffae225a815669dacf8fd5a9d3d6f4e3083f90e4a0ed811ffdf718166ca850655d679af428c7f4067a4973802ecdeb1e580351d2f796d9')

prepare() {
  cd $pkgname-$pkgver
  export RUSTUP_TOOLCHAIN=stable
  cargo fetch --locked --target "$(rustc -vV | sed -n 's/host: //p')"
}

build() {
  cd $pkgname-$pkgver
  export RUSTUP_TOOLCHAIN=stable
  export CARGO_TARGET_DIR=target
  cargo build --frozen --release --features default
}

check() {
  cd "$pkgname-$pkgver"
  export RUSTUP_TOOLCHAIN=stable
  export XDG_RUNTIME_DIR="$(mktemp -d)"
  export RAYON_NUM_THREADS=1  # multithreading issues: https://github.com/YaLTeR/niri/issues/953
  cargo test --all --exclude niri-visual-tests --frozen
}

package() {
  cd $pkgname-$pkgver
  install -vDm 755 {target/release/$pkgname,resources/$pkgname-session} -t "$pkgdir/usr/bin/"
  install -vDm 644 resources/$pkgname{.service,-shutdown.target} -t "$pkgdir/usr/lib/systemd/user/"
  install -vDm 644 resources/$pkgname.desktop -t "$pkgdir/usr/share/wayland-sessions/"
  install -vDm 644 resources/$pkgname-portals.conf -t "$pkgdir/usr/share/xdg-desktop-portal/"
  install -vDm 644 resources/default-config.kdl README.md -t "$pkgdir/usr/share/doc/$pkgname/"
}
