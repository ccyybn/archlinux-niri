# Maintainer: David Runge <dvzrv@archlinux.org>

pkgname=niri
pkgver=0.1.6
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
sha512sums=('ed83a62552e3a7b049ec03aff2e8e191eb383584f1789f15fe94cce8df847a0de7b3917bd5b64fa1c8e6392de52bb81caa92472a3e40df97ac1b5562b881dca2')
b2sums=('17ab734d3412b4da4e0eca7a3dcbbb7210140ef9ae7a6d4bcd9ca7177128073c9385a59776ab2903e4ac70e158a5860c310519f0b5768721bbee483bad9ad0f4')

prepare() {
  cd $pkgname-$pkgver
  export RUSTUP_TOOLCHAIN=stable
  cargo fetch --locked --target "$(rustc -vV | sed -n 's/host: //p')"
}

build() {
  cd $pkgname-$pkgver
  export RUSTUP_TOOLCHAIN=stable
  export CARGO_TARGET_DIR=target
  cargo build --frozen --release
}

check() {
  cd "$pkgname-$pkgver"
  export RUSTUP_TOOLCHAIN=stable
  cargo test --frozen --all-features
}

package() {
  cd $pkgname-$pkgver
  install -vDm 755 {target/release/$pkgname,resources/$pkgname-session} -t "$pkgdir/usr/bin/"
  install -vDm 644 resources/$pkgname{.service,-shutdown.target} -t "$pkgdir/usr/lib/systemd/user/"
  install -vDm 644 resources/$pkgname.desktop -t "$pkgdir/usr/share/wayland-sessions/"
  install -vDm 644 resources/$pkgname-portals.conf -t "$pkgdir/usr/share/xdg-desktop-portal/"
  install -vDm 644 resources/default-config.kdl README.md -t "$pkgdir/usr/share/doc/$pkgname/"
}
